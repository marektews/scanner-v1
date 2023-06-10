<script setup>
const emit = defineEmits(['startScanner'])
const props = defineProps(['qrcode'])
</script>

<template>
    <div>
        {{ props.qrcode }}
    </div>
</template>