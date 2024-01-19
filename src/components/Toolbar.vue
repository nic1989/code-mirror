<template>
    <div class="toolbar">
      <div class="item">
        <label for="language">language:</label>
        <select
          name="language"
          id="language"
          :disabled="disabled"
          :value="config.language"
          @change="handleSelectLanguage"
        >
          <option :value="option" :key="option" v-for="option in languages">
            {{ option }}
          </option>
        </select>
      </div>
    </div>
</template>
<script>
    export default {
      props: {
        disabled: {
          type: Boolean,
          default: false
        },
        config: {
          type: Object,
          required: true
        },
        languages: {
          type: Array,
          required: true
        }
      },
      setup (props, context) {
        function handleSelectLanguage (event) {
            context.emit('language', event.target.value);
        }

        return {
            handleSelectLanguage
        }
      }
    }
  </script>
  
  <style lang="scss" scoped>
    .toolbar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 3rem;
      padding: 0 1em;
  
      .item {
        display: inline-flex;
        align-items: center;
  
        label {
          display: inline-block;
          margin-right: 0.2em;
        }
      }
  
      input,
      button,
      select {
        margin: 0;
      }
  
      select {
        max-width: 3em;
        max-width: 8em;
      }
    }
  </style>