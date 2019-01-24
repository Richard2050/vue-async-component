<template>
  <div class="componentC">
    <h2>组件c 默认被全局样式影响</h2>
    <h2 class="title">组件c 里的class="title"</h2>
    <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="textareaValue">
    </el-input>
    <img src="../assets/big.png" />
  </div>
</template>

<script>
export default {
    name: 'ComponentB',
    props: {
        url: String,
        textarea: {
            type: String,
            default: ''
        }
    },
    computed: {
        textareaValue: {
            get() {
                return this.textarea
            },
            set(val) {
                this.$emit('update:textarea', val)
            }
        }
    }
}
</script>

<style lang="scss" scoped>
.title {
    line-height: 2;
    color: yellow;
}
</style>
