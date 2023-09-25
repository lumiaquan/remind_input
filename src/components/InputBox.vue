<template>
  <div class="input-box-container">
    <div
      ref="editor"
      class="input-box"
      contenteditable="true"
      @input="inputChange"
      @focus="onFocus"
      @blur="onBlur"
    />
    <el-select
      v-show="selectShow"
      ref="nameSelect"
      v-model="person"
      filterable
      placeholder="请选择"
      @change="selectChange"
    >
      <el-option
        v-for="item in options"
        :key="item.id"
        :label="item.name"
        :value="item.id"
      />
    </el-select>
  </div>
</template>

<script>

export default {
  name: 'InputBox',
  data() {
    return {
      content: '',
      isBlur: false,
      person: null,
      options: [
        { id: 1, name: 'Jack', url: 'https://www.baidu.com' },
        { id: 2, name: 'Tom', url: 'https://www.google.com' },
        { id: 3, name: 'Rose', url: 'https://www.bing.com' }
      ],
      selectShow: false,
      // 光标选中区域，用于保存光标
      range: null,
      replacePosition: null,
      atIndex: 0
    }
  },
  watch: {
    content(val) {
      if (this.isBlur) {
        this.$refs.editor.innerHTML = val
      }
    }
  },
  methods: {
    inputChange(e) {
      this.content = this.$refs.editor.innerHTML
      // 用户输入@符时
      if (e.data === '@') {
        const range = window.getSelection().getRangeAt(0) // 获取当前光标
        const position = range.getBoundingClientRect()
        console.log(window.getSelection())
        this.replacePosition = window.getSelection().anchorOffset
        const select = document.querySelector('.el-select')
        select.style.top = position.y + 'px'
        select.style.left = position.x + 5 + 'px'
        this.selectShow = true
        this.$refs.nameSelect.toggleMenu()
      } else {
      // 输入其他字符时
        this.selectShow && this.$refs.nameSelect.toggleMenu()
        this.selectShow = false
      }
    },
    onFocus() {
      this.isBlur = false
    },
    onBlur() {
      this.isBlur = true
      this.range = this.saveSelection()
    },
    // 保存光标
    saveSelection() {
      const selection = window.getSelection()
      if (selection && selection.getRangeAt(0)) {
        return window.getSelection().getRangeAt(0)
      }
    },
    // 恢复光标
    restoreSelection() {
      if (this.range) {
        const selection = window.getSelection()
        selection.removeAllRanges()
        selection.addRange(this.range)
      }
    },
    selectChange(id) {
      this.atIndex++
      this.restoreSelection()
      const range = window.getSelection().getRangeAt(0)
      // 光标移动到最后
      range.collapse(false)
      // 插入的span标签contenteditable为false时可以让标签整体删除
      const name = this.findName(id)
      const insetText = `<span contenteditable="false" id="at-${this.atIndex}" class="remind-name">@${name}</span>`
      const node = range.createContextualFragment(insetText)
      const child = node.lastChild
      range.insertNode(node)
      if (child) {
        range.setEndAfter(child)
        // range.setEndBefore(child)
      }
      const sel = window.getSelection()
      sel.removeAllRanges()
      sel.addRange(range)
      this.selectShow = false
      this.$nextTick(() => {
        if (!/@<span contenteditable="false"/.test(this.$refs.editor.innerHTML)) return
        console.log(this.$refs.editor.innerHTML)
        this.$refs.editor.innerHTML = this.$refs.editor.innerHTML.replace(/@<span contenteditable="false"/, '<span contenteditable="false"')
        const el = document.getElementById(`at-${this.atIndex}`)
        const range = document.createRange()
        const sel = window.getSelection()
        // 将光标重新定位到自定义标签后面
        range.setEndAfter(el)
        range.collapse(false)

        sel.removeAllRanges()
        sel.addRange(range)
      })
    },
    findName(id) {
      return this.options.find(item => item.id === id).name
    }
  }
}

</script>

<style scoped lang='less'>
.input-box-container {
  .input-box {
    width: 300px;
    height: 200px;
    outline: none;
    border: 1px solid #6e6e6e;
    padding: 10px;
  }
  /deep/.el-select {
    position: absolute;
    width: 100px;
    .el-input__inner {
      height: fit-content;
      line-height: 30px;
    }
    .el-select__caret {
      line-height: 30px;
    }
  }
}
</style>
<style>
.remind-name {
  color: skyblue;
}
</style>
