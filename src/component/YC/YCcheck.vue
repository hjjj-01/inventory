<template>
    <div class="box">
        <!-- <h3>库位多货提交</h3> -->
        <span>请扫描箱号</span>
        <el-input placeholder="请输入箱号" v-model="commitData.boxNo" ></el-input>
        <div>
            <el-button type="primary" @click="commit">查询</el-button>
            <el-button type="primary" @click="router.back()">返回</el-button>
        </div>

        <el-table border :data="tableData" height="500px">
            <el-table-column label="sku" width="120px" prop="sku"/>
            <el-table-column label="款号" width="150px" prop="shop_name"/>
            <el-table-column label="数量" width="70px" prop="realNumber"/>
        </el-table>
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    import { ElMessage } from 'element-plus'
    import axios from 'axios'
    import router from '@/router'
    const toCommit = () => {
        router.push('/YCcommit')
    }

    const commitData = ref({
        boxNo: '',
    })
    const tableData = ref([])

    const commit = () =>{
        if(commitData.value.boxNo === ''){
            ElMessage.error('请完整填写信息')
            return
        }
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/inventory/YcBoxCheck', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('查询成功')
            tableData.value = res.data.data;
        })
        .catch(err => {
            ElMessage.error('查询失败')
            console.log(err)
        })
    }

   
</script>

<style scoped>
    .box{
        width: 350px;
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
        margin-top: 1px;
        margin-bottom: 3px;
    }
    .el-table{
        width: 400px;
        padding: 10px;
    }
</style>
