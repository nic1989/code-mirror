<template>
  <div class="about">
    <VCodeMirror 
      value=""
      :mode="mode"
      :options="cmOption"
      @update:value="handleUpdate"
    />
  </div>
</template>

<script>
  import { ref } from 'vue';
  import { VCodeMirror } from 'vue3-code-mirror';
  export default {
    components: {
      VCodeMirror
    },
    setup () {
      const mode = ref({ name: 'python', json: true })
      const cmOption = ref({
        tabSize: 2,
        lineNumbers: true
      })

      function handleUpdate (val) {
        console.log(val)
      }

      return {
        mode,
        cmOption,
        handleUpdate
      }
    }
  }
</script>
<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    margin-top: 50px;
    align-items: center;
  }
}
</style>
