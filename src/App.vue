<script setup>
import { onMounted } from 'vue'
import { Html5Qrcode } from 'html5-qrcode'

onMounted(() => {
    Html5Qrcode.getCameras()
    .then((devices) => {
        if(devices && devices.length) {
            let cameraId = devices[0].id
            const scanner = new Html5Qrcode("reader")
            scanner.start(
                cameraId, 
                {
                    fps: 10,
                    qrbox: {
                        width: 350,
                        height: 350
                    }
                },
                onScanSuccess, 
                onScanFailure
            )
            .catch(err => console.error(`Code scan start error = ${err}`))
        }
    })
    .catch(err => console.error(`getCameras() error = ${err}`))
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
        <div id="reader" width="600px" height="600px"></div>
    </main>
</template>

<style scoped>
</style>
