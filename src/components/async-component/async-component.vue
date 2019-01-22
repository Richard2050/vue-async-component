<template>
  <component :is="mode" v-bind="$attrs" v-on="$listeners">
  </component>
</template>

<script>
import Axios from 'axios'
import Vue from 'vue'

const customMark = 'dynamicDependencies'
const markReg = new RegExp(customMark + '\\s*:\\s*\\[.+\\]', 'g')
const dependenceReg = /\[.+\]/
let loaded = (window.loadedDynamicDependencies = window.loadedDynamicDependencies || {})

const denpendenceUrlMap = {
    echarts: 'http://ripplescloud.dev.crpharm.com:5500/node_modules/echarts/dist/echarts.min.js',
    d3: 'http://ripplescloud.dev.crpharm.com:5500/node_modules/d3/dist/d3.min.js'
}

function loadDynamicDependencies(sourceCode) {
    let matchInfo, dependencies
    let allDependencies = []
    while ((matchInfo = markReg.exec(sourceCode))) {
        dependencies = matchInfo[0].match(dependenceReg)
        dependencies = new Function(`return ${dependencies}`)()
        allDependencies = allDependencies.concat(dependencies)
    }

    let asyncArray = []
    allDependencies.forEach(item => {
        if (!loaded[item]) {
            loaded[item] = 1
            let url = denpendenceUrlMap[item]
            if (!url) {
                throw new Error(`动态依赖 ${item} 地址有误!`)
            } else {
                asyncArray.push(
                    Axios.get(url).then(res => {
                        /* eslint no-new-func: 0 */
                        new Function(res.data).call(window)
                    })
                )
            }
        }
    })

    return Axios.all(asyncArray)
}

export default {
    name: 'AsyncComponent',
    props: {
        // 父组件提供请求地址
        url: {
            type: String,
            default: ''
        }
    },
    inheritAttrs: true,
    data() {
        return {
            resData: '',
            mode: ''
        }
    },
    watch: {
        url: {
            immediate: true,
            async handler(val, oldVal) {
                const url = this.url
                if (!url) return
                // Cache 缓存 根据 url 参数
                let cache = (window._AsyncComponentCache = window._AsyncComponentCache || {})
                let comName, res
                if (!cache[url]) {
                    res = await Axios.get(url)
                    try {
                        await loadDynamicDependencies(res.data)
                        /* eslint no-new-func: 0 */
                        res = new Function(`return ${res.data}`)()
                    } catch (e) {
                        this.mode = ''
                        throw new Error('动态组件解析失败!')
                    }

                    comName = 'async' + window.btoa(url).replace(/=/g, '')
                    Vue.component(comName, res)
                    cache[url] = comName
                } else {
                    comName = cache[url]
                    await Promise.resolve()
                }

                this.mode = comName
            }
        }
    }
}
</script>
