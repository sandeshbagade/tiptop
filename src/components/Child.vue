<template>
  <div
    class="editor"
    v-bind:style="{border: editorBorder}"
  >
     <editor-menu-bubble class="menububble" :editor="editor" @hide="hideLinkMenu" v-slot="{ commands, isActive, getMarkAttrs, menu }">
      <div
        class="menububble"
        :class="{ 'is-active': menu.isActive }"
        :style="`left: ${menu.left}px; bottom: ${menu.bottom}px;`"
      >

        <form class="menububble__form" v-if="linkMenuIsActive" @submit.prevent="setLinkUrl(commands.link, linkUrl)">
          <input class="menububble__input" type="text" v-model="linkUrl" placeholder="https://" ref="linkInput" @keydown.esc="hideLinkMenu"/>
          <button class="menububble__button" @click="setLinkUrl(commands.link, null)" type="button">
            <icon name="remove" />
          </button>
        </form>

        <template v-else>
          <button
            class="menububble__button"
            @click="showLinkMenu(getMarkAttrs('link'))"
            :class="{ 'is-active': isActive.link() }"
          >
            <span>{{ isActive.link() ? 'Update Link' : 'Add Link'}}</span>
            <icon name="link" />
          </button>
        </template>

      </div>
    </editor-menu-bubble>
    <editor-menu-bar
      :editor="editor"
      :autoFocus="false"
      v-slot="{ commands, isActive }"
    >
    
      <div
        v-if="editable"
        class="menubar"
      >
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.bold() }"
          @click="commands.bold"
        >B
          <font-awesome-icon icon="bold" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.italic() }"
          @click="commands.italic"
        >I
          <font-awesome-icon icon="italic" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.underline() }"
          @click="commands.underline"
        >U
          <font-awesome-icon icon="underline" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.code() }"
          @click="commands.code"
        >code
          <font-awesome-icon icon="code" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.paragraph() }"
          @click="commands.paragraph"
        >p
          <font-awesome-icon icon="paragraph" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.heading({ level: 1 }) }"
          @click="commands.heading({ level: 1 })"
        >
          H1
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.heading({ level: 2 }) }"
          @click="commands.heading({ level: 2 })"
        >
          H2
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.heading({ level: 3 }) }"
          @click="commands.heading({ level: 3 })"
        >
          H3
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.bullet_list() }"
          @click="commands.bullet_list"
        >List-ul
          <font-awesome-icon icon="list-ul" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.ordered_list() }"
          @click="commands.ordered_list"
        >List-ol
          <font-awesome-icon icon="list-ol" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.blockquote() }"
          @click="commands.blockquote"
        >QR
          <font-awesome-icon icon="quote-right" />
        </button>
 
        <button
          class="menubar__button"
          :class="{ 'is-active': isActive.code_block() }"
          @click="commands.code_block"
        >FC
          <font-awesome-icon icon="file-code" />
        </button>
 
        <!-- <button
          class="menubar__button"
          @click="commands.horizontal_rule"
        >
          <icon name="hr" />
        </button> -->
 
        <button
          class="menubar__button"
          @click="commands.undo"
        >undo
          <font-awesome-icon icon="undo" />
        </button>
 
        <button
          class="menubar__button"
          @click="commands.redo"
        >redo
          <font-awesome-icon icon="redo" />
        </button>
          <button
          class="menubar__button"
          @click="onc()"
        >Image
          <icon name="image" />
        </button>
        <button type="button" :class="{ 'is-active': isActive.blockquote() }" @click="commands.blockquote">
        Blockquote
      </button>
 
      </div>
    </editor-menu-bar>
    
    <editor-content
      class="editor__content"
      v-bind:style="{'min-height': computedMinHeight}"
      :editor="editor"
    />
   <Icon name="code" />
   
  </div>
</template>
<script>
import { Editor, EditorContent, EditorMenuBar, EditorMenuBubble } from 'tiptap'

import {
  Blockquote,
  CodeBlock,
  HardBreak,
  Heading,
  //   HorizontalRule,
  OrderedList,
  BulletList,
  ListItem,
  TodoItem,
  TodoList,
  Bold,
  Code,
  Italic,
  Link,
  Image,
  //   Strike,
  Underline,
  History,
} from 'tiptap-extensions'
import Icon from './Icon'
export default {
  name: 'TextEditor',
  components: {
    EditorContent,
    EditorMenuBar,
    EditorMenuBubble,

    Icon
  },
  props: {
    value: {
      type: String,
      default: '',
    },
    editable: {
      type: Boolean,
      default: false,
    },
    minHeight: {
      type: String,
      default: '100px'
    }
  },
  data () {
    return {
      editor: null,
      editorChange: false,
      editorBorder: '0px',
      computedMinHeight: '',
      linkUrl: null,
    linkMenuIsActive:false,
    }
  },
  mounted () {
    this.editor = new Editor({
      autoFocus: true,
      extensions: [
        new Blockquote(),
        new BulletList(),
        new CodeBlock(),
        new HardBreak(),
        new Heading({ levels: [1, 2, 3] }),
        //   new HorizontalRule(),
        new ListItem(),
        new OrderedList(),
        new TodoItem(),
        new TodoList(),
        new Link(),
        new Image(),
        new Bold(),
        new Code(),
        new Italic(),
        //   new Strike(),
        new Underline(),
        new History(),
        //   new Placeholder({
        //     emptyNodeClass: 'is-empty',
        //     emptyNodeText: 'Write something …',
        //     showOnlyWhenEditable: true,
        //   }),
      ],
      
      //   content: this.editorContent,
         linkUrl: null,
      linkMenuIsActive: false,
      editable: this.editable,
     // eslint-disable-next-line
      onUpdate: ({ getJSON, getHTML }) => {
        // eslint-disable-next-line
        console.log("callin gon update")
        this.editorChange = true;
        this.$emit('input', getHTML())
      },
    })
    this.editor.setContent(this.value)
    if (this.editable) {
      // this.editorBorder = "0.5px solid rgba (0, 0, 0, 0.2)"
      this.editorBorder = '0.5px solid rgba(0, 0, 0, 0.2)'
      this.computedMinHeight = this.minHeight
    } else {
      this.editorBorder = '0px'
      this.computedMinHeight = ''
    }
  },
  watch: {
    value: function (newContent) {
      if (!this.editorChange) {
        this.editor.setContent(newContent, true)
      }
      this.editorChange = false;
    },
    editable: function (newEditable) {
      // eslint-disable-next-line
      console.log("Setting editable to: " + newEditable)
      this.editor.setOptions({
        editable: newEditable
      })
      if (newEditable) {
        // this.editorBorder = "0.5px solid rgba (0, 0, 0, 0.2)"
        this.editorBorder = '0.5px solid rgba(0, 0, 0, 0.2)'
        this.computedMinHeight = this.minHeight
      } else {
        this.editorBorder = '0px'
        this.computedMinHeight = ''
      }
    }
  },
  beforeDestroy () {
    // Always destroy your editor instance when it's no longer needed
    this.editor.destroy()
  },
   showLinkMenu(attrs) {
      this.linkUrl = attrs.href
      this.linkMenuIsActive = true
      this.$nextTick(() => {
        this.$refs.linkInput.focus()
      })
    },
    hideLinkMenu() {
      this.linkUrl = null
      this.linkMenuIsActive = false
    },
    setLinkUrl(command, url) {
      command({ href: url })
      this.hideLinkMenu()
    },
    showImagePrompt(command) {
      alert("sd");
      const src = prompt('Enter the url of your image here')
      if (src !== null) {
        command({ src })
      }
    },
   
}
</script>
<style scoped>
.menubar {
  border-bottom: 1px solid silver;
  /* background: white; */
  z-index: 10;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  overflow: visible;
}
.menubar__button {
  font-weight: 700;
  display: inline-flex;
  border: 0;
  cursor: pointer;
  
  margin:5px;
}
.editor__content {
  overflow-wrap: break-word;
  word-wrap: break-word;
  padding: 20px 8px 4px 14px;
  line-height: 1;
  outline: none;
}
 
.editor {
  background: white;
  color: black;
  background-clip: padding-box;
  border-radius: 4px;
  /* border: 0.5px solid rgba(0, 0, 0, 0.2); */
  padding: 5px 0;
  margin-bottom: 23px;
}
:focus {
  outline: 0px;
}
p {
  font-size: 16px;
  line-height: 20px;
}
</style>
<style>
.editor__content .ProseMirror-focused {
  outline: none;
}
.editor__content pre {
  padding: 0.7rem 1rem;
  border-radius: 5px;
  background: rgb(80, 80, 80);
  color: #fff;
  font-size: 1rem;
  overflow-x: auto;
  line-height: 1.5;
}
</style>