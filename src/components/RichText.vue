<template>
  <div class="my-5">
    <div>
      <h2 class="h2">Editor</h2>
    </div>
    <Button @click="setIsActive" class="btn my-3 p-3 bg-blue-400 rounded text-white font-bold">Edit</Button>
    <div class="container flex justify-center">
      <div v-if="isActive" class="mt-5 popup">
        <div class="flex flex-row items-center my-2">
          <label>Change Background Color</label>
          <input v-model="color" id="color" type="color">
        </div>
        <div ref="editor" :style="{background: color}" class="text-left">
        </div>
        <button @click="Close" class="btn p-3 bg-gray-400 text-center mt-3 text-white">Close</button>
      </div>
    </div>
    <div class="result p-3" v-if="content" v-html="content" :style="{background: color}">
    </div>
  </div>
</template>

<script>
import "quill/dist/quill.bubble.css";
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import Quill from "quill";
import {customIcons, toolbarConfig} from '@/constants/richText-constants.js'

export default {
  name: "App",
  components: {},
  data() {
    var icons = Quill.import("ui/icons");
    var self = this

    /* Implementing icons from richText-constants.js */
    icons["undo"] = customIcons["undo"];
    icons["redo"] = customIcons["redo"];
    icons["link"] = customIcons["link"]
    icons["image"] = customIcons["image"]
    icons["code"] = customIcons["code"]
    icons["code-block"] = customIcons["codeBlock"]
    icons["blockquote"] = customIcons['blockquote']
    icons["list"]["ordered"] = customIcons["listOrdered"]
    icons["list"]["bullet"] = customIcons["listBullet"]
    icons["clean"] = customIcons["clean"]
    icons["align"][""] = customIcons["align"]

    return {
      toolbar_settings: {
        // init config
        ...toolbarConfig,
        handlers: {
          redo() {
            self.editor.history.redo();
          },
          undo() {
            self.editor.history.undo();
          }
        },
      },
      content: '',
      isActive: false,
      color: ''
    };
  },
  mounted() {
    this.initialize()
  },
  methods: {
    initialize() {
      setTimeout(() => {
        let options = {
          debug: this.debug,
          modules: {
            history: {
              delay: 1000,
              maxStack: 100,
              userOnly: false
            },
            toolbar: this.toolbar_settings
            // this.toolbar,
          },
          readOnly: false,
          theme: "snow"
        };

        this.editor = new Quill(this.$refs.editor, options);

        if(this.content) {
          const editor = document.getElementsByClassName('ql-editor')
          editor[0].innerHTML = this.content
        }

        this.editor.on('text-change', () => {
          let html = this.$refs.editor.children[0].innerHTML
          if (html === '<p><br></p>') html = ''
          this.submit(html)
        })

      }, 1)
    },
    submit(text) {
      this.content = text
    },
    setIsActive() {
      this.isActive = !this.isActive
      this.initialize()
    },
    Close() {
      this.isActive = false
    }
  }
};
</script>

<style>
@import "~quill/dist/quill.bubble.css";
@import "~quill/dist/quill.snow.css";
@import "~quill/dist/quill.core.css";

.ql-size-small {
  font-size: 0.75em;
}

.ql-size-large {
  font-size: 1.5em;
}

.ql-size-huge {
  font-size: 2.5em;
}

.popup {
  position: fixed;
  padding: 50px;
  border: 1px solid #c4c4c4;
  border-radius: 10px;
  top: 200px;
  background: white;
  display: flex;
  align-content: center;
  flex-direction: column;
  box-shadow: 0px 0px 50px 10px rgba(0, 0, 0, 0.25);
}

#color {
  width: 25px;
}

</style>
