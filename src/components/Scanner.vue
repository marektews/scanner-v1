<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'
import { Html5Qrcode } from 'html5-qrcode'

const emit = defineEmits(['qrcode'])

let scanner = null
const qrCode = ref('')
watch(qrCode, (nv) => {
    emit('qrcode', nv)
})

onMounted(() => {
    scanner = new Html5Qrcode("reader")
    startDetector()
})

onBeforeUnmount(() => {
    stopDetector()
})

function startDetector() {
    scanner.start(
        { facingMode: "environment" }, 
        {
            fps: 20,
            qrbox: {
                width: 450,
                height: 450
            }
        },
        (decodedText, decodedResult) => {
            console.log(`Code matched = ${decodedText}`, decodedResult)
            if(decodedResult.result.format.format === 0) {
                qrCode.value = decodedText
            }
        }, 
        // (error) => {
        //     console.warn(`Code scan error = ${error}`)
        // }
    )
    // .catch(err => console.error(`Code scan start error = ${err}`))
}

function stopDetector() {
    if(scanner != null) {
        scanner.stop()
        scanner = null
    }
}
</script>

<template>
    <div id="reader" width="600px" height="600px" />
</template>