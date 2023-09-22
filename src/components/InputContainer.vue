<template>
  <div>
    <DivEditable v-model="content"/>
    <input type="text" v-model="content">
    <div @click="changeValue">changeValue</div>
  </div>
</template>

<script>
import DivEditable from '@/components/DivEditable.vue'

export default {
  name: 'InputContainer',
  components: { DivEditable },
  data () {
    return {
      content: ''
    }
  },
  watch: {
    content (val) {
      console.log('父组件接收到的值：', val)
    }
  },
  methods: {
    contentChange (e) {
      this.content = e.target.innerHTML
      if (e.data === '@') {
        this.handleSelectName()
      }
    },
    changeValue () {

    }
  }
}

</script>

<style scoped lang='less'>

</style>
