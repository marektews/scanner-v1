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
    <div class="my-cnt">
        <div class="result-layout" :class="{'bg-success': result == 200, 'bg-danger': result != 200}">
            <div>{{ pass_nr }}</div>
            <div>{{ regnum }}</div>
            <div class="error-info">{{ errorInfo }}</div>
        </div>
        <div class="btns-layout">
            <button class="btn btn-primary btn-lg" @click="$emit('startScanner')">
                <i class="fa-solid fa-qrcode fa-4x"/>
                <div style="margin-top: 0.5rem;">Skanuj następny kod za {{ counter }} sekund</div>
            </button>
        </div>
    </div>
</template>

<style scoped>
.my-cnt {
    width: 100vw;
    height: 100vh;
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: 1fr 1fr;
}
.result-layout {
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    gap: 2pt;
    align-items: center;
    font-size: 4rem;
    font-weight: bold !important;
    color: black;
    padding: 3rem;
}

.error-info {
    font-size: 1rem;
}

.btns-layout {
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    gap: 2pt;
    align-items: center;
    justify-content: center;
}
</style>