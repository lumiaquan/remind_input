<template>
  <div
    ref="editor"
    class="input-container"
    :contenteditable="true"
    @input="inputChange"
    @blur="onBlur"
    @focus="onFocus"
  >
  </div>
</template>

<script>
export default {
  name: 'DivEditable',
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  watch: {
    value (val) {
      if (this.isBlur) {
        this.$refs.editor.innerHTML = val
      }
    }
  },
  data () {
    return {
      isBlur: false
    }
  },
  methods: {
    inputChange () {
      this.$emit('input', this.$refs.editor.innerHTML)
    },
    onBlur () {
      this.isBlur = true
    },
    onFocus () {
      this.isBlur = false
    }
  }
}

</script>

<style scoped lang='less'>
.input-container {
  height: 100px;
  width: 300px;
  padding: 10px;
  border: 1px solid #6e6e6e;
  outline: none;
}
</style>
