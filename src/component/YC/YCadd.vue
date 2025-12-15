<template>
    <div class="box">
        <!-- <h3>库位多货提交</h3> -->
        <h3 style="color: red;">上架</h3>
        <div>箱号</div>
        <el-input placeholder="请输入箱号" v-model="commitData.boxNo" @change="toSku()"></el-input>
        <div>sku</div>
        <el-input placeholder="请输入sku" v-model="sku" ref="skuInput" @input="inputSku"></el-input>
       
        <div>
            <el-button type="primary" @click="commit">上架</el-button>
            <el-button type="primary" @click="clear">撤销</el-button>
            <el-button type="primary" @click="router.back()">返回</el-button>

        </div>
        <div>
            <el-table :data="tableDate">
                <el-table-column label="sku" prop="sku" width="190" height="50"></el-table-column>
                <el-table-column label="数量" prop="realNumber"></el-table-column>
            </el-table>
        </div>
        
    </div>
</template>
<script setup>
    import { ref, computed } from 'vue'
    import { ElMessage } from 'element-plus'
    import axios from 'axios'
    import router from '@/router'
    
    const skuInput = ref(null)
    const sku = ref('')
    const commitData = ref({
        boxNo: '',
    })
    const tableDateDel = ref([])
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
    const inputSku = (sku1) => {
        tableDateDel.value.push({
            sku: sku1.trim(),
            realNumber: 1
        })
        sku.value = ''
    }
    const tableDate = computed(() => {
        const transfer = []
        tableDateDel.value.forEach(item => {
            if(transfer[item.sku]){
                transfer[item.sku].realNumber += item.realNumber
            }else{
                transfer[item.sku] = {
                    sku: item.sku,
                    realNumber: item.realNumber
                }
            }
        })
        return Object.values(transfer)
    })
    const clear = () => {
        tableDateDel.value.pop()
    }
    const commit = () =>{
        if(commitData.value.boxNo === ''){
            ElMessage.error('请输入箱号')
            return
        }
        if(tableDate.value.length === 0){
            ElMessage.error('请输入上架商品')
            return
        }
        commitData.value.dateTime = getDate()
        commitData.value.tableDate = tableDate.value.map(item => ({
            sku: item.sku,
            realNumber: item.realNumber,
            dateTime: commitData.value.dateTime,
            boxNo: commitData.value.boxNo
        }))
        console.log(commitData.value)
        axios.post('http://134.175.18.3:8083/api/inventory/YcBoxAdd', commitData.value.tableDate, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            commitData.value.boxNo = ''
            tableDateDel.value = []
            ElMessage.success('提交成功')
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
    }
    const toSku = () => {
        skuInput.value.focus()
    }

   
</script>

<style scoped>
    .box{
        width: 300px;
        height: 450px;
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
        width:29%;
        margin-top: 10px;
        margin-bottom: 10px;
    }
    .el-table{
        height: 200px;
    }
</style>
