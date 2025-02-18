<script setup lang="ts">
import { ref } from 'vue'
import { LucideSun, LucideWaves, LucideWind } from 'lucide-vue-next'
import { IInput } from '~/components/ui/input'

const WEATHER_API = "f5e4ab512a95994a0a7079755ddb10ce"
const WEATHER_URL = "https://api.openweathermap.org/data/2.5/weather?units=metric&q="

const searchQuery = ref("")
const city = ref("Kyiv")
const temperature = ref("22°C")
const humidity = ref("50%")
const windSpeed = ref("15 km/h")
const loading = ref(false)
const errorMessage = ref("")

async function checkWeather() {
    if (!searchQuery.value) return

    loading.value = true
    errorMessage.value = ""

    try {
        const response = await fetch(`${WEATHER_URL}${searchQuery.value}&appid=${WEATHER_API}`)
        const data = await response.json()

        if (data.cod !== 200) {
            throw new Error(data.message || "City not found")
        }

        city.value = data.name
        temperature.value = Math.round(data.main.temp) + "°C"
        humidity.value = data.main.humidity + "%"
        windSpeed.value = data.wind.speed + " km/h"
    } catch (error) {
        errorMessage.value = error.message
    } finally {
        loading.value = false
    }
}
</script>

<template>
    <section id="hero" class="w-full">
        <Container>
            <div class="flex h-28 flex-col items-center justify-center gap-2">
                <div class="flex gap-2">
                    <IInput v-model="searchQuery" placeholder="Enter the location" container-class="w-full max-w-sm" />
                    <RippleButton @click="checkWeather" :disabled="loading">
                        {{ loading ? "Checking..." : "Check" }}
                    </RippleButton>
                </div>
                <p v-if="errorMessage" class="text-red-500">{{ errorMessage }}</p>
            </div>
            
            <div class="flex flex-col items-center justify-center gap-2 border p-8 rounded-lg">
                <LucideSun :size="100"/>
                <h2 class="mt-10 text-5xl">{{ temperature }}</h2>
                <h2 class="text-5xl">{{ city }}</h2>

                <div class="flex gap-10 mt-10">
                    <div class="flex items-center gap-4">
                        <LucideWaves />
                        <div class="grid">
                            <span>{{ humidity }}</span>
                            <p>Humidity</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-4">
                        <LucideWind />
                        <div class="grid">
                            <span>{{ windSpeed }}</span>
                            <p>Wind Speed</p>
                        </div>
                    </div>
                </div>
            </div>
        </Container>
    </section>
</template>
