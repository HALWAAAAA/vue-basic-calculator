<template>
    <h4>Your Balance</h4>
    <h1 id="balance">zł{{total}}</h1>

</template>

<script setup>
import { defineProps } from 'vue';
const props = defineProps({
    total:{
        type: Number,
        required: true
    }
});
</script>