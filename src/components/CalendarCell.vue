<template>
    <td v-for="day in days">
{{day.day}}
    </td>
</template>

<script>
    export default {
        props: {
            days: Array,
        }
    }
</script>

<style scoped>

</style>