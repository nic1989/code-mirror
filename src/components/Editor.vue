<template>
    <div class="editor">
      <div class="main">
        <codemirror
          v-model="codee"
          :style="{
            width: preview ? '50%' : '100%',
            height: config.height,
            backgroundColor: '#fff',
            color: '#333'
          }"
          placeholder="Please enter the code."
          :extensions="extensions"
          :autofocus="config.autofocus"
          :disabled="config.disabled"
          :indent-with-tab="config.indentWithTab"
          :tab-size="config.tabSize"
          @update="handleStateUpdate"
          @ready="handleReady"
          @focus="log('focus', $event)"
          @blur="log('blur', $event)"
        />
        <pre
          v-if="preview"
          class="code"
          :style="{ height: config.height, width: preview ? '50%' : '0px' }"
          >{{ codee }}</pre
        >
      </div>
      <div class="divider"></div>
      <div class="footer">
        <div class="buttons">
          <button class="item" @click="togglePreview">
            <span>Preview</span>
            <i class="iconfont" :class="preview ? 'icon-eye' : 'icon-eye-close'"></i>
          </button>
          <button class="item" @click="handleUndo">Undo</button>
          <button class="item" @click="handleRedo">Redo</button>
        </div>
        <div class="infos">
          <span class="item">Spaces: {{ config.tabSize }}</span>
          <span class="item">Length: {{ state.length }}</span>
          <span class="item">Lines: {{ state.lines }}</span>
          <span class="item">Cursor: {{ state.cursor }}</span>
          <span class="item">Selected: {{ state.selected }}</span>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    import { defineComponent, reactive, shallowRef, computed, watch, onMounted } from 'vue'
    import { redo, undo } from '@codemirror/commands'
    import { Codemirror } from 'vue-codemirror'
  
    export default defineComponent({
      name: 'vue-codemirror-example',
      title: 'Web IDE example',
      props: {
        config: {
          type: Object,
          required: true
        },
        code: {
          type: String,
          required: true
        },
        language: Function
      },
      components: {
        Codemirror
      },
      setup(props) {
        const log = console.log
        const codee = shallowRef(props.code)
        const extensions = computed(() => {
          const result = []
          if (props.language) {
            result.push(props.language())
          }
          return result
        })
  
        const preview = shallowRef(false)
        const togglePreview = () => {
          preview.value = !preview.value
        }
  
        const cmView = shallowRef()
        const handleReady = (view) => {
          cmView.value = view
        }
  
        const handleUndo = () => {
          undo({
            state: cmView?.value?.state,
            dispatch: cmView?.value?.dispatch
          })
        }
  
        const handleRedo = () => {
          redo({
            state: cmView.value?.state,
            dispatch: cmView.value?.dispatch
          })
        }
  
        const state = reactive({
          lines: null,
          cursor: null,
          selected: null,
          length: null
        })
  
        const handleStateUpdate = (viewUpdate) => {
          // selected
          const ranges = viewUpdate.state.selection.ranges
          state.selected = ranges.reduce((plus, range) => plus + range.to - range.from, 0)
          state.cursor = ranges[0].anchor
          // length
          state.length = viewUpdate.state.doc.length
          state.lines = viewUpdate.state.doc.lines
          // log('viewUpdate', viewUpdate)
        }
  
        onMounted(() => {
          watch(
            () => props.code,
            (_code) => {
                codee.value = _code
            }
          )
        })
  
        return {
          log,
          codee,
          extensions,
          preview,
          state,
          togglePreview,
          handleReady,
          handleStateUpdate,
          handleRedo,
          handleUndo
        }
      }
    })
  </script>
  
  <style lang="scss" scoped>
    .editor {
      .divider {
        height: 1px;
        background-color: #CCC;
      }
  
      .main {
        display: flex;
        width: 100%;
  
        .code {
          width: 30%;
          height: 100px;
          margin: 0;
          padding: 0.4em;
          overflow: scroll;
          border-left: 1px solid #CCC;
          font-family: monospace;
        }
      }
  
      .footer {
        height: 3rem;
        padding: 0 1em;
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 90%;
  
        .buttons {
          .item {
            margin-right: 1em;
            display: inline-flex;
            justify-content: center;
            align-items: center;
            background-color: transparent;
            border: 1px dashed #CCC;
            font-size: 12px;
            color: #333;
            cursor: pointer;
            .iconfont {
              margin-left: 10px;
            }
            &:hover {
              color: #FF0000;
              border-color: #000;
            }
          }
        }
  
        .infos {
          .item {
            margin-left: 2em;
            display: inline-block;
            font-feature-settings: 'tnum';
          }
        }
      }
    }
  </style>