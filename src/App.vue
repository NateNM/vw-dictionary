<template>
  <form @submit.prevent="getDefinitions()">
    <input placeholder="myWord" type="text" id="word" v-model="word">
    <button type="submit">Get definition</button>
  </form>
  <p v-if="!validQuery">Error: The word you are searching for does not exist. Please try a differnt word.</p>
  <div v-for="definition in definitionArray" :key="definition">
    <div>{{ definition }}</div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
  name: 'App',
  setup() {
    let validQuery = ref(true)
    const definitionArray = ref([])
    const word = ref("");

    async function getDefinitions() {
      console.log(word.value);
      definitionArray.value.splice(0, definitionArray.value.length)
      validQuery.value = true;

      try{
        const url =  "https://api.dictionaryapi.dev/api/v2/entries/en/" + word.value;
        const { data } = await axios.get(url);
        console.log(data);
        data.forEach(({ meanings }) =>
          meanings.forEach(({ definitions }) =>
            definitions.forEach(({ definition }) => {
              if (typeof definition === "string") {
                definitionArray.value.push(definition)
                console.log(definition);
              }
            })
          )
        );
      } catch (error){
          validQuery.value = false;
      }
    }

    return { word, getDefinitions, definitionArray, validQuery };
  }
}
</script>

<style>

</style>
