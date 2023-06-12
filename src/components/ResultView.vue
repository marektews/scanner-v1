<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const emit = defineEmits(['startScanner'])
const props = defineProps(['qrcode'])

const timer = ref(undefined)
const counter = ref(5)
const result = ref(undefined)
const errorInfo = ref('')
const pass_nr = computed(() => props.qrcode.split('-')[0])
const regnum = computed(() => {
    let d = new Date()
    switch(d.getDay()) {
        case 5: return props.qrcode.split('-')[1]
        case 6: return props.qrcode.split('-')[2]
        case 0: return props.qrcode.split('-')[3]
    }
    return props.qrcode.split('-')[1]
})

onMounted(() => {
    fetch(`/api/srp/check?data=${props.qrcode}`)
    .then(response => {
        result.value = response.status
        if(response.status === 200) {
            // OK
            console.log('OK:', props.qrcode)

        }
        else {
            // błąd
            console.error('Error:', props.qrcode, 'status:', response.status)
        }

        timer.value = setInterval(() => {
            counter.value -= 1
            if(counter.value === 0)
                emit('startScanner')
        }, 1000)
        return response.text()
    })
    .then(d => errorInfo.value = d)
})

onBeforeUnmount(() => {
    clearInterval(timer.value)
    timer.value = undefined
})
</script>

<template>
    <div class="my-cnt" :class="{'bg-success': result == 200, 'bg-danger': (result != 200 && result != undefined)}">
        <div class="result-layout" >
            <div class="passnr">{{ pass_nr }}</div>
            <div class="regnum">{{ regnum }}</div>
            <div class="error-info">{{ errorInfo }}</div>
        </div>
        <div class="btns-layout">
            <button class="btn btn-primary btn-lg" @click="$emit('startScanner')">
                <i class="fa-solid fa-qrcode fa-2x"/>
                <div style="margin-top: 0.5rem;">Skanuj następny kod za {{ counter }} sekund</div>
            </button>
        </div>
    </div>
</template>

<style scoped>
.my-cnt {
    width: 100%;
    height: 100%;
}
.result-layout {
    position: fixed;
    top: 0;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    gap: 2pt;
    align-items: center;
    justify-content: center;
    color: black;
    padding: 5%;
}

.passnr, .regnum {
    font-size: 3rem;
    font-weight: bold !important;
}

.error-info {
    font-size: 1rem;
}

.btns-layout {
    position: fixed;
    bottom: 1rem;
    padding: 2rem;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    gap: 2pt;
    align-items: center;
    justify-content: center;
}
</style>