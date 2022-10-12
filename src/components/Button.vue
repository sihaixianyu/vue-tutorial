<template>
    <el-button :color="color" @click="$emit('btn-click')">
        {{ text }}
    </el-button>
</template>

<script>
export default {
    name: "Button",
    props: {
        text: String,
        color: String,
    },
}
</script>