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
        :value="item"
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
      range: null
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
      return window.getSelection().getRangeAt(0)
    },
    // 移动光标
    removeSelection() {
      if (this.range) {
        const selection = window.getSelection()
        selection.addRange(this.range)
        selection.removeAllRanges()
      }
    },
    selectChange(person) {
      const replacePosition = window.getSelection().anchorOffset
      const frondStr = this.content.substring(0, replacePosition - 1)
      const endStr = this.content.substring(replacePosition)
      // 插入的span标签contenteditable为false时可以让标签整体删除
      const replaceText = `<span contenteditable="false" class="remind-name">@${person.name}</span>`
      this.content = frondStr + replaceText + endStr
      this.selectShow = false
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
