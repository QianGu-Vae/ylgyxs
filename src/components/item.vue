<template>
    <div class="symbol" :style="{
        transform: `translateX(${x}%) translateY(${y}%)`,
        opacity: status < 2 ? 1 : 0,
    }">
        <div class="symbol-inner" :style="{ backgroundColor: isCover ? '#999' : 'white' }">
            <img style="width:100% ;" :src="icon" alt="">
        </div>
    </div>
</template>
<script setup lang="ts">
defineProps({
    isCover: Boolean,
    status: { type: Number, required: true },
    x: Number,
    y: Number,
    icon: String,
});
</script>