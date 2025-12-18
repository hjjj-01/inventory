<template>
    <div class="box">
        <h3>好春盘点异常提交</h3>
        <span>请输入登记人员</span>
        <el-input placeholder="请输入登记人员" v-model="commitData.operator" ></el-input>
        <span>请扫描库位号</span>
        <el-input ref="stockInput" placeholder="请扫描库位条码输入" v-model="commitData.stock" @change="focusSkuInput"></el-input>
        <span>请扫描商品SKU</span>
        <el-input ref="skuInput" placeholder="请扫描SKU条码输入" v-model="commitData.sku" @change="focusNumberInput"></el-input>
        <span>请输入商品数量</span>
        <el-input ref="numberInput" placeholder="请输入商品数量" v-model="commitData.numbers" @input="validate(commitData.numbers)" @change="focusRemarkInput"></el-input>
        <span>请输入备注</span>
        <el-input ref="remarkInput" placeholder="请输入备注" v-model="commitData.remark" ></el-input>
        <el-button type="primary" @click="commit">提交</el-button>
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    import { ElMessage } from 'element-plus'
import axios from 'axios'

    const commitData = ref({
        operator: localStorage.getItem('HCoperator') || '',
        stock: localStorage.getItem('HCstock') || '',
        sku: '',
        numbers: '',
        remark: '',
        dateTime:'',
        complete:'未完成'
    })
    
    const stockInput = ref(null)
    const skuInput = ref(null)
    const numberInput = ref(null)
    const remarkInput = ref(null)
    
    const focusSkuInput = () => {
        skuInput.value.focus()
    }

    const focusNumberInput = () => {
        numberInput.value.focus()
    }
    const focusRemarkInput = () => {
        remarkInput.value.focus()
    }

    const getDate =() =>{
        const date = new Date();
        const year = date.getFullYear();
        const month = date.getMonth() + 1;
        const day = date.getDate();
        const hour = date.getHours();
        const minute = date.getMinutes();
        const second = date.getSeconds();
        return `${year}-${month}-${day} ${hour}:${minute}:${second}`;
    }

    const commit = () =>{
        localStorage.setItem('HCoperator', commitData.value.operator)
        localStorage.setItem('HCstock', commitData.value.stock)
        if(commitData.value.operator === '' || commitData.value.stock === '' || commitData.value.sku === '' || commitData.value.numbers === ''){
            ElMessage.error('请完整填写信息')
            return
        }
        commitData.value.dateTime = getDate();
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/HC/InventoryCommit', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('提交成功')
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
    }

    const validate = (number) =>{
        commitData.value.numbers = number.replace(/[^\d,-]/g, '')
    }
</script>

<style scoped>
    .box{
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        background-color: rgb(224, 238, 233);
    }
    .el-input{
        width: 90%;
        margin-bottom: 20px;
    }
    .el-button{
        width: 50%;
        margin-top: 10px;
    }
</style>
