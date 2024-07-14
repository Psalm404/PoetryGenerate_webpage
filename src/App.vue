<template>
<div class="container">
    <div class="flavor-text">
        <div class="text" style="color: #606266">古诗生成</div>
    </div>
    <div class="choose_box">
        <div class="cover"></div>
        <el-input style="width: 250px" placeholder="请输入汉字词语" v-model="word"></el-input>
        <el-select v-model="value" placeholder="请选择类型" style="width: 150px;">
            <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
            </el-option>
        </el-select>

        <div style="display: flex; flex-direction: column;">
            <el-radio v-model="radio" label="1">以文本藏头</el-radio>
            <el-radio v-model="radio" style="margin-top: 10px;" label="2">以文本开头</el-radio>
        </div>
        <el-button type="primary" style="width: 100px;" @click="generate">生成</el-button>
    </div>
    <div v-if="value === '随机生成'" class="temperature">
        <div style="color:#606266">温度值 </div>
        <el-slider v-model="temperature" :min="0.1" :max="0.9" :step="0.1" style="width:200px;"></el-slider>
        <div style = "color:#606266; font-size:14px; font-family:Arial, Helvetica, sans-serif"> Tips:温度值越高，随机性越强</div>
    </div>

    <div style="padding-bottom: 250px;"></div>
    <div class="poetry_box" v-html="formattedPoetry"></div>
</div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            word: '',
            radio: '',
            value: '',
            temperature: 0.5, 
            randomOption: [{
                value: '随机生成',
                label: '随机生成'
            }],
            allOptions: [{
                value: '五言诗',
                label: '五言诗'
            }, {
                value: '七言诗',
                label: '七言诗'
            }, {
                value: '随机生成',
                label: '随机生成'
            }],
            options: [{
                value: '五言诗',
                label: '五言诗'
            }, {
                value: '七言诗',
                label: '七言诗'
            }, {
                value: '随机生成',
                label: '随机生成'
            }],
            poetry: ''
        };
    },
    watch: {
        radio(newVal) {
            if (newVal === '2') {
                this.options = this.randomOption;
                this.value = '随机生成';
            } else {
                this.options = this.allOptions;
            }
        }
    },
    computed: {
        formattedPoetry() {
            return this.poetry.replace(/。/g, '。<br>');
        }
    },
    methods: {
        generate() {
            const dataToSend = {
                word: this.word,
                radio: this.radio,
                value: this.value,
                temperature: this.temperature // Include temperature in the data to send
            };

            axios.post('http://localhost:5001/generate_poem', dataToSend)
                .then(response => {
                    console.log(response.data);
                    this.poetry = response.data.poem;
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
    }
};
</script>

<style>
.choose_box {
    display: flex;
    gap: 10px;
}

.choose_box cover {
    filter: blur(10px);
    position: absolute;
    z-index: -1;
}

.text {
    font-size: 3em;
    margin-bottom: 70px;
}

.poetry_box {
    position: absolute;
    top: 395px;
    left: 50%;
    transform: translateX(-50%);
    max-height: 300px;
    overflow-y: auto;
    font-size: 20px;
    color: darkslategray;
}

.temperature {
    width: 500px;
    position: absolute;
    top: 355px;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
}

.container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    /* 或者使用 min-height 替代 height */
    width: 100vw;
    background-color: #8EC5FC;
    background-image: linear-gradient(62deg, #8EC5FC 0%, #E0C3FC 100%);
}

.temperature_selector {
    margin-top: 20px;
    display: flex;
    align-items: center;
}

.el-slider {
    margin-right: 10px;
}
</style>
