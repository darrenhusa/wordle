<script setup lang="ts">
import { DEFEAT_MESSAGE, VICTORY_MESSAGE, WORD_SIZE } from '@/settings';
import englishWords from "@/englishWordsWith5Letters.json"
import { computed, ref, triggerRef } from 'vue';

defineProps({
  wordOfTheDay: {
    type: String,
    validator: (wordGiven: string) => wordGiven.length === WORD_SIZE
    && wordGiven.toUpperCase() === wordGiven && englishWords.includes(wordGiven)
  }
})
const guessInProgress = ref<string|null>(null)
const guessSubmitted = ref("")

const formattedGuessInProgress = computed<string>({
  get(){
    return guessInProgress.value ?? "" // ?? is the null coalesing operator
  },
  set(rawValue: string){
    guessInProgress.value = rawValue
        .slice(0, WORD_SIZE)
        .toUpperCase()
        .replace(/[^A-Z]+/gi, "")

    triggerRef(formattedGuessInProgress)
  },

})

function onSubmit() {
  if (!englishWords.includes(formattedGuessInProgress.value)) {
    return;
  }
  guessSubmitted.value = formattedGuessInProgress.value

}
</script>

<template>
  <input v-model="formattedGuessInProgress"
         type="text"
         :maxlength="WORD_SIZE"
         @keydown.enter="onSubmit"
  >
  <p v-if="guessSubmitted.length > 0"
     v-text="guessSubmitted === wordOfTheDay ? VICTORY_MESSAGE : DEFEAT_MESSAGE" />
</template>

