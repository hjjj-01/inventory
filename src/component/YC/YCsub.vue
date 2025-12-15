<template>
    <div class="box">
        <!-- <h3>库位多货提交</h3> -->
        <h3 style="color: red;">下架</h3>
        <div>箱号</div>
        <el-input placeholder="请输入箱号" v-model="commitData[0].boxNo" ></el-input>
        <div>sku</div>
        <el-input placeholder="请输入sku" v-model="commitData[0].sku" @change="toNumber()"></el-input>
        <div>数量</div>
        <el-input ref="numberInput" placeholder="请输入sku数量" v-model="inputNumber" @input="NumberInput(inputNumber)"></el-input>
        <div>
            <el-button type="primary" @click="commit">下架</el-button>
            <el-button type="primary" @click="router.back()">返回</el-button>
        </div>
        
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    import { ElMessage } from 'element-plus'
    import axios from 'axios'
    import router from '@/router'

    
    const numberInput = ref(null)
    const inputNumber = ref('')
    const commitData = ref([{
        boxNo: '',
        sku: '',
        realNumber: '',
        dateTime:''
    }])
    const getDate = () => {
        let date = new Date()
        let year = date.getFullYear()
        let month = date.getMonth() + 1
        let day = date.getDate()
        let hour = date.getHours()
        let minute = date.getMinutes()
        let second = date.getSeconds()
        return year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second
    }

    const commit = () =>{
        if(commitData.value[0].boxNo === '' || commitData.value[0].sku === '' || inputNumber.value === ''){
            ElMessage.error('请完整填写信息')
            return
        }
        commitData.value[0].dateTime = getDate()
        commitData.value[0].realNumber = -Number(inputNumber.value)
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/inventory/YcBoxAdd', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('提交成功')
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
    }
    const NumberInput = (number) => {
        commitData.value.realNumber = Number(number.replace(/[^\d]/g, ''))
    }
    const toNumber = () => {
        numberInput.value.focus()
    }

   
</script>

<style scoped>
    .box{
        width: 300px;
        height: 430px;
        display: flex;
        flex-direction: column;
        align-items: center;
        background-color: rgb(224, 238, 233);
    }
    .el-input{
        width: 90%;
        margin-bottom: 10px;
    }
    .el-button{
        width: 45%;
        margin-top: 15px;
        margin-bottom: 10px;
    }
</style>
