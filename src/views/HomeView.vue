<template>
  <div class="example">
    <toolbar
      :config="config"
      :disabled="loading"
      :languages="Object.keys(languages)"
      @language="ensureLanguageCode"
    />
    <div class="divider"></div>
    <div class="loading-box" v-if="loading">
      Loading...
    </div>
    <editor
      v-else-if="currentLangCode"
      :config="config"
      :language="currentLangCode.language"
      :code="currentLangCode.code"
    />
  </div>
</template>

<script>
  import { defineComponent, reactive, computed, shallowRef, onBeforeMount } from 'vue'
  import languages from '../utils/languages'
  import Toolbar from '../components/Toolbar.vue'
  import Editor from '../components/Editor.vue'

  export default defineComponent({
    name: 'vue-codemirror-example',
    title: 'Example Source Code',
    components: {
      Toolbar,
      Editor
    },
    setup() {
      const config = reactive({
        disabled: false,
        indentWithTab: true,
        tabSize: 2,
        autofocus: true,
        height: 'auto',
        language: 'javascript',
        theme: 'default'
      })

      const loading = shallowRef(false)
      const langCodeMap = reactive(new Map())
      const currentLangCode = computed(() => langCodeMap.get(config.language))
      const currentTheme = shallowRef('dracula')

      const ensureLanguageCode = async (targetLanguage) => {
        config.language = targetLanguage
        loading.value = true
        const delayPromise = () => new Promise((resolve) => window.setTimeout(resolve, 260))
        if (langCodeMap.has(targetLanguage)) {
          await delayPromise()
        } else {
          const [result] = await Promise.all([languages[targetLanguage](), delayPromise()])
          console.log('result', result);
          langCodeMap.set(targetLanguage, result.default)
        }
        loading.value = false
      }

      // HACK: Make sure the first screen the user sees is the loading placeholder
      loading.value = true
      onBeforeMount(() => {
        // init default language & code
        ensureLanguageCode(config.language)
      })

      return {
        loading,
        config,
        languages,
        currentTheme,
        currentLangCode,
        ensureLanguageCode
      }
    }
  })
</script>

<style lang="scss" scoped>
  .example {
    .divider {
      height: 1px;
      background-color: #CCC;
    }

    .loading-box {
      width: 100%;
      min-height: 20rem;
      max-height: 60rem;
      $page-height: 2rem * 2 + 3.2rem + 3rem;
      /* editor-border * 2 */
      height: calc(100vh - 200px - $page-height - 2px);
    }
  }
</style>