<template>
  <div>
    <div>{{inputValue}}</div>
    <InputPhoneMask
        :value="inputValue"
        @input="e => (inputValue = e.value)"
    />
  </div>
</template>

<script setup>
import InputPhoneMask from "./components/InputPhoneMask.vue";
import { ref } from "vue";

const inputValue = ref('')
</script>


