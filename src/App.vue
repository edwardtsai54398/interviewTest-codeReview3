<script setup>
import GDBInput from './components/GDBInput.vue'
</script>

<template>
  <GDBInput @update:modelValue="modelValue" :prefix="'E-mail'" :placeholder="'請輸入信箱'"/>
  <GDBInput :type="'password'" :prefix="'Password'"/>
  <GDBInput :disabled="true"/>
  <GDBInput :type="'number'"/>
</template>

<style scoped>

</style>
