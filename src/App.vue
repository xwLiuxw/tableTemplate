<template>
    <div id="app">
        <div>api:<input v-model="api" /></div>
        <textarea
            v-model="value"
            style="width:500px;height:500px"
            placeholder='JAVA注解例子：
            @ApiModelProperty(value = "商品sku") 
            private String skuCode;'
        />
        <button @click="change">转换</button>
        <textarea v-model="tableConfig" style="width:500px;height:500px" />
        <textarea v-model="template" style="width:500px;height:500px" />
    </div>
</template>

<script>
export default {
    name: 'app',
    data () {
        return {
            value: '',
            tableConfig: '',
            temp: [],
            template: '',
            api: '',
            demo: ''
        }
    },
    created () {
        this.$http.get('demo.txt').then(res => {
            this.demo = res.body
        })
    },
    methods: {
        change () {
            this.value.split('@')
            let arr = this.value.split('@')
            this.temp = []
            arr.map(item => {
                if (item) {
                    let label = item.match(/(?<=").*?(?=")/)[0], prop = item.match(/(\S*);/)[1]
                    let obj = {
                        label,
                        prop,
                        width: item.match(/(?<=").*?(?=")/)[0].length * 14 + 40,
                    }
                    if (label.indexOf("金额")!=-1 || label.indexOf("价")!=-1 || label.indexOf("价格")!=-1) {
                        obj["formatter"] = "this.amountFormat"
                    }
                    this.temp.push(obj)
                }
            })
            this.tableConfig = JSON.stringify(this.temp)
            this.template = this.demo.replace(/##/g, this.tableConfig)
            this.template = this.template.replace(/&&/g, this.api)
        }
    }
}
</script>

<style>
#app {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    display: flex;
    justify-content: space-around;
    align-items: center;
}
</style>
