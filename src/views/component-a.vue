<template>
  <div class="component">
    <h2 :class="$style.title">组件A</h2>
    <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="textareaValue">
    </el-input>
    <img src="../assets/logo.png" />
    <commponent-c></commponent-c>
    <div class="echarts-box" ref="echarts-box"></div>
  </div>
</template>

<script>
// var echarts = require('echarts')
import echarts from 'echarts'
import commponentC from './component-c'
export default {
    dynamicDependencies: ['echarts'],
    name: 'ComponentA',
    components: {
        commponentC
    },
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

    /deep/ .componentC {
        h2 {
            background-color: blue;
            font-size: 30px;
        }
    }
}
</style>

<style module>
.title {
    font-size: 24px;
    color: red;
}
</style>

