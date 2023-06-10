<script setup>
import { ref } from 'vue'
import Scanner from './components/Scanner.vue'
import Result from './components/Result.vue'

const mode = ref(0)
const qrcode = ref('')
</script>

<template>
    <main>
        <Scanner v-if="mode === 0"
            @qrcode="qrcode = $event; mode = 1"
        />
        <Result v-else
            :qrcode = qrcode
        />
    </main>
</template>

<style scoped>
</style>
