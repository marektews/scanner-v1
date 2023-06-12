<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'
import { Html5Qrcode } from 'html5-qrcode'

const emit = defineEmits(['qrcode'])

const scanner = ref(null)
const qrCode = ref('')
watch(qrCode, (nv) => {
    emit('qrcode', nv)
})

onMounted(() => {
    startDetector()
})

onBeforeUnmount(() => {
    stopDetector()
})

function startDetector() {
    scanner.value = new Html5Qrcode("reader")
    scanner.value.start(
        { facingMode: "environment" }, 
        { fps: 5 },
        (decodedText, decodedResult) => {
            console.log(`Code matched = ${decodedText}`, decodedResult)
            if(decodedResult.result.format.format === 0) {
                qrCode.value = decodedText
            }
        }, 
    )
}

function stopDetector() {
    if(scanner.value != null) {
        scanner.value.stop()
        scanner.value = null
    }
}
</script>

<template>
    <div class="scanner-layout">
        <div id="reader" />

        <div class="btns-layout">
            <button v-if="scanner != null" class="btn btn-lg btn-danger" @click="stopDetector">
                <i class="fa-solid fa-power-off" />
                <div>Oszdzędzanie baterii</div>
            </button>
            <button v-else class="btn btn-lg btn-success" @click="startDetector">
                <i class="fa-solid fa-power-off" />
                <div>Włącz skaner</div>
            </button>
        </div>
    </div>
</template>

<style scoped>
.scanner-layout {
    /* width: 100vw; */
    /* height: 100vh; */
}

.btns-layout {
    position: fixed;
    right: 10px;
    bottom: 10px;
    /* width: 100%;
    margin: 0;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 100; */
}

#reader {
    /* width: 100%; */
    /* height: 50%; */
}
</style>