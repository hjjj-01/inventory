<template>
    <div class="box">
        <h3>抽盘库位提交</h3>
        <span>请输入登记人员</span>
        <el-input placeholder="请输入登记人员" v-model="commitData.operator" ></el-input>
        <span>请扫描库位号</span>
        <el-input ref="stockInput" placeholder="请扫描库位条码输入" v-model="commitData.stock" @change="focusSysInput" ></el-input>
        <span>请输入系统数量</span>
        <el-input ref="sysInput" placeholder="请输入系统数量" v-model="commitData.sysNumber" @change="focusRealInput" @input="validateSys(commitData.sysNumber)"></el-input>
        <span>请输入实际数量</span>
        <el-input ref="realInput" placeholder="请输入实际数量" v-model="commitData.realNumber" @input="validate(commitData.realNumber)" @change="focusRemarkInput"></el-input>
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
        operator: localStorage.getItem('Reoperator') || '',
        stock: localStorage.getItem('Restock') || '',
        sysNumber: '',
        realNumber: '',
        remark: '',
        dateTime:''
    })
    
    const stockInput = ref(null)
    const sysInput = ref(null)
    const realInput = ref(null)
    const remarkInput = ref(null)
    
    const focusSysInput = () => {
        sysInput.value.focus()
    }

    const focusRealInput = () => {
        realInput.value.focus()
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
        localStorage.setItem('Reoperator', commitData.value.operator)
        localStorage.setItem('Restock', commitData.value.stock)
        if(commitData.value.operator === '' || commitData.value.stock === '' || commitData.value.sysNumber === '' || commitData.value.realNumber === ''){
            ElMessage.error('请完整填写信息')
            return
        }
        commitData.value.dateTime = getDate();
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/inventory/ReCommit', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('提交成功')
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
    }

    const validate = (number) =>{
        commitData.value.realNumber = number.replace(/[^\d,-]/g, '')
    }
    const validateSys = (number) =>{
        commitData.value.sysNumber = number.replace(/[^\d,-]/g, '')
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
