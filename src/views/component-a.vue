<template>
  <div class="component">
    <h2 class="title">组件A</h2>
    <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="textareaValue">
    </el-input>
    <div class="echarts-box" ref="echarts-box"></div>
  </div>
</template>

<script>
var echarts = require('echarts')

export default {
    need: ['echarts'],
    name: 'ComponentA',
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
    },
    mounted() {
        // 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(this.$refs['echarts-box'])
        // 绘制图表
        myChart.setOption({
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            xAxis: {
                data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
            },
            yAxis: {},
            series: [
                {
                    name: '销量',
                    type: 'bar',
                    data: [5, 20, 36, 10, 10, 20]
                }
            ]
        })
    }
}
</script>

<style lang="scss" scoped>
.component {
    .title {
        line-height: 2;
    }
    .echarts-box {
        height: 300px;
    }
}
</style>
