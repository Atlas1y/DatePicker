<template>
    <div id="myDatePicker" :class="inline ? 'inline-picker' : 'block-picker'">
        <el-date-picker
        v-model="value[0]"
        type="date"
        placeholder="开始日期"
        format="yyyy-MM-dd"
        value-format="yyyy-MM-dd"
        ref="beginDate"
        :picker-options="pickerOptions"
        @change="$emit('update:value',value);handleChange()"
        @focus="setDateInputEvent('myDatePicker',value)"
        style="margin-right: 10px;">
        </el-date-picker>
        <span>{{ this.seperater }}</span>
        <el-date-picker
        v-model="value[1]"
        type="date"
        placeholder="结束日期"
        format="yyyy-MM-dd"
        value-format="yyyy-MM-dd"
        ref="endDate"
        :picker-options="pickerOptions"
        @change="$emit('update:value',value);handleChange()"
        @focus="setDateInputEvent('myDatePicker',value)"
        style="margin-left: 10px;">
        </el-date-picker>
    </div>
</template>
<script>
    export default {
        data(){
            return{
            }
        },
        computed: {
            pickerOptions(){ // 日期选择器的快捷选项，可以手动修改和添加自定义的快捷选项
                let _this = this;
                return {
                    shortcuts: [{
                        text: "最近一周",
                        onClick(picker) {
                            const end = new Date()
                            const start = new Date()
                            start.setTime(start.getTime() - 1000 * 3600 * 24 * 7)
                            _this.$emit('update:value',[_this.formatDate(start,'yyyy-MM-dd'),_this.formatDate(end,'yyyy-MM-dd')])
                        }
                    }, {
                        text: "最近1月",
                        onClick(picker) {
                            const end = new Date()
                            const start = new Date()
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
                            _this.$emit('update:value',[_this.formatDate(start,'yyyy-MM-dd'),_this.formatDate(end,'yyyy-MM-dd')])
                        }
                    }, {
                        text: "最近3月",
                        onClick(picker) {
                            const end = new Date()
                            const start = new Date()
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
                            _this.$emit('update:value',[_this.formatDate(start,'yyyy-MM-dd'),_this.formatDate(end,'yyyy-MM-dd')])
                        }
                    }, {
                        text: "最近1年",
                        onClick(picker) {
                            const end = new Date()
                            const start = new Date()
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 365)
                            _this.$emit('update:value',[_this.formatDate(start,'yyyy-MM-dd'),_this.formatDate(end,'yyyy-MM-dd')])
                        }
                    }, {
                        text: "今年以来",
                        onClick(picker) {
                            const end = new Date()
                            const start = new Date(end.getFullYear(), 0, 1)
                            _this.$emit('update:value',[_this.formatDate(start,'yyyy-MM-dd'),_this.formatDate(end,'yyyy-MM-dd')])
                        }
                    }]
                }
            }
        },
        props: {
            // 绑定的参数名，在父组件中设置":value.sync='propName'"字段即可实现propName与子组件value的双向绑定
            value: Array, // 日期参数，格式为['beginDate','endDate']
            seperater: {  // 分隔符
                type: String,
                default: '~'
            },
            inline: { // 设置这个组件的样式是否为行内元素
                type: String,
                default: false
            }
            // 还可以传入其他参数，根据是否在父组件添加.sync修饰符以及子组件传递update:propName事件消息决定是否为双向绑定
            // propName1: typeName
            // propName2: typeName
        },
        methods:{
            formatDate: function(date, format) {
                const pad = (num) => num.toString().padStart(2, '0');

                const year = date.getFullYear();
                const month = pad(date.getMonth() + 1);
                const day = pad(date.getDate());
                const hour = pad(date.getHours());
                const minute = pad(date.getMinutes());
                const second = pad(date.getSeconds());

                return format
                    .replace('yyyy', year)
                    .replace('MM', month)
                    .replace('dd', day)
                    .replace('HH', hour)
                    .replace('mm', minute)
                    .replace('ss', second);
            },
            disposeData: function(str){ // 这个方法可以将在日期选择器中手动输入的内容自动识别并转化为yyyy-MM-dd格式，支持通过正则表达式自定义添加可识别的格式
                // yyyyMMdd 格式转化
                if (/^\d{8}$/.test(str)) {
                    // 将 yyyyMMdd 格式转换为 yyyy-MM-dd 格式
                    return str.replace(/^(\d{4})(\d{2})(\d{2})$/, '$1-$2-$3');
                }

                // yyyy-MM-dd 格式转化
                if (/^\d{4}-\d{2}-\d{2}$/.test(str)) {
                    // 已经是 yyyy-MM-dd 格式，直接返回
                    return str;
                }

                // 如果格式不匹配，抛出错误
                throw new Error('Invalid date format');
            },
            setDateInputEvent: function(e,data){ // 手动输入日期的格式转化器实现
				var _this = this;
                var beginData = document.getElementById(e).getElementsByTagName("input")[0];
                var endData = document.getElementById(e).getElementsByTagName("input")[1];
                beginData.addEventListener("blur", inTapEvent);
                endData.addEventListener("blur", outTapEvent);
                function inTapEvent() {
                    beginData.value = disposeData(beginData.value);
                    Vue.set(data, 0, beginData.value);
                }
                function outTapEvent() {
                    endData.value = disposeData(endData.value);
                    Vue.set(data, 1, endData.value);
                }

                return data;
			},
            handleChange(){
                this.$emit('dateChange'); // 改变日期后，返回一个dateChange事件消息
            }
        }
    }
</script>
<style>
.inline-picker{
    display: inline-flex;
    justify-content: flex-end;
    align-items: center;
}
.block-picker{
    display: flex;
    justify-content: flex-end;
    align-items: center;
}
</style>