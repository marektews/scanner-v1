<script setup>
import { onMounted } from 'vue'
import { Html5QrcodeScanner } from 'html5-qrcode'

onMounted(() => {
    const scanner = new Html5QrcodeScanner("reader", {
        fps: 10,
        qrbox: {
            width: 350,
            height: 350
        }
    })
    scanner.render(onScanSuccess, onScanFailure)
})

function onScanSuccess(decodedText, decodedResult) {
    console.log(`Code matched = ${decodedText}`, decodedResult);
}

function onScanFailure(error) {
    console.warn(`Code scan error = ${error}`);
}
</script>

<template>
    <main>
        <div id="reader" width="600px"></div>
    </main>
</template>

<style scoped>
</style>
