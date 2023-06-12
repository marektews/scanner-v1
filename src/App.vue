<script setup>
import { ref } from 'vue'
import ScannerView from './components/ScannerView.vue'
import ResultView from './components/ResultView.vue'

const mode = ref(0)
const qrcode = ref('')
</script>

<template>
    <main>
        <ScannerView v-if="mode === 0"
            @qrcode="qrcode = $event; mode = 1"
        />
        <ResultView v-else
            :qrcode = qrcode
            @startScanner="mode = 0; qrcode = ''"
        />
    </main>
</template>

<style scoped>
main {
    /* min-width: 100vw; */
    /* min-height: 100vh; */
}
</style>
