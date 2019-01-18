<template>
  <component :is="mode" v-bind="$attrs" v-on="$listeners">
  </component>
</template>

<script>
import Axios from 'axios';
import Vue from 'vue';

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
        };
    },
    watch: {
        url: {
            immediate: true,
            async handler(val, oldVal) {
                const url = this.url;
                if (!url) return;
                // Cache 缓存 根据 url 参数
                let cache = window._AsyncComponentCache;
                if (!cache) {
                    cache = window._AsyncComponentCache = {};
                }

                let comName, res;
                if (!cache[url]) {
                    res = await Axios.get(url);
                    try {
                        res = new Function(`return ${res.data}`)();
                    } catch (e) {
                        console.error('动态组件解析失败!');
                        this.mode = '';
                        return;
                    }

                    comName = 'async' + window.btoa(url).replace(/=/g, '');
                    Vue.component(comName, res);
                    cache[url] = comName;
                } else {
                    comName = cache[url];
                    await Promise.resolve();
                }

                this.mode = comName;
            }
        }
    }
};
</script>
