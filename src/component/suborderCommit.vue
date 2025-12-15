<template>
    <div class="box">
        <h3>次单区登记</h3>
        <span>请扫描库位号</span>
        <el-input placeholder="请扫描库位条码输入" v-model="commitData.stock" @change="focusISCInput"></el-input>
        <span>请扫描入仓码</span>
        <el-input ref="inStockCodeInput" placeholder="请扫描入仓码" v-model="commitData.inStockCode" @change="focusBtn"></el-input>
        <el-button ref="commitBtn" type="primary" @click="commit">提交</el-button>
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    import { ElMessage } from 'element-plus'
    import axios from 'axios'

    const commitData = ref({
        stock: '',
        inStockCode: '',
    })
    
    const inStockCodeInput = ref(null)
    const commitBtn = ref(null)
    
    const focusISCInput = () => {
        inStockCodeInput.value.focus()
    }
    const focusBtn = () => {
        commitBtn.value.focus()
    }



    const commit = () =>{
        if(commitData.value.stock === '' || commitData.value.inStockCode === ''){
            ElMessage.error('请完整填写信息')
            return
        }
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/subOrder/commit', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('提交成功')
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
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
