<template>
  <div class="flex space-x-2">
    <input
      v-for="(digit, index) in pinLength"
      :key="index"
      :ref="`pin-${index}`"
      v-model="pins[index]"
      @input="(event) => handleInput(index, event as InputEvent)"
      @keydown="(event) => handleKeyDown(index, event as KeyboardEvent)"
      @paste="handlePaste"
      @focus="handleFocus(index)"
      :maxlength="1"
      :autofocus="index === 0 ? true : false"
      :type="secretMode ? 'password' : 'text'"
      class="w-12 h-12 text-center border rounded focus:outline-none"
      :class="{ 'bg-gray-300': secretMode }"
      :readonly="secretMode"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";

const pinLength = ref(5); // Default pin length
const pins = ref(new Array(pinLength.value).fill(""));
const secretMode = ref(false);

const handleInput = (index: number, event: InputEvent) => {
  const value = (event.target as HTMLInputElement).value;
  // Validate input to allow only digits 1-9
  if (/^[1-9]$/.test(value)) {
    pins.value[index] = value;
    if (value && index < pinLength.value - 1) {
      const nextInput = (event.target as HTMLInputElement)
        .nextElementSibling as HTMLInputElement | null;
      nextInput && nextInput.focus();
    }
  } else {
    pins.value[index] = "";
  }
};

const handleKeyDown = (index: number, event: KeyboardEvent) => {
  if (event.key === "Backspace" && !pins.value[index] && index > 0) {
    const preInput = (event.target as HTMLInputElement)
      .previousElementSibling as HTMLInputElement | null;
    preInput && preInput.focus();
  }
};

const handlePaste = (event: ClipboardEvent) => {
  event.preventDefault();
  const pasteData = event.clipboardData?.getData("text");
  if (pasteData && pasteData.length <= pinLength.value) {
    pasteData.split("").forEach((char, index) => {
      pins.value[index] = char;
    });
  }
};

const handleFocus = (index: number) => {
  if (!pins.value[index]) {
    pins.value[index] = "";
  }
};

watch(secretMode, (newValue, oldValue) => {
  console.log(newValue, oldValue);
})
</script>
