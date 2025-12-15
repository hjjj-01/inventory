<template>
    <div class="box">
        <div class="oprate-box">
            <div class="title">操作<span class="sector">好春后台系统</span></div>
            <div>
                <el-button @click="getStorageList">查询</el-button>
                <!-- <el-button @click="reset()">重置</el-button> -->
                <!-- <el-button @click="changeName()">修改名称</el-button> -->
                <!-- <el-button @click="changeNumber()">修改数量</el-button> -->
                <el-button @click="delStorage()">单个删除</el-button>
                <el-button @click="delStorageAll()">整箱删除</el-button>
                <el-button @click="delStorageChaos()">混箱删除</el-button>
                <el-button @click="nextStep()">导出</el-button>
            </div>
        </div>
       <!-- <div class="check-box"> -->
            <!-- <div class="title">查询条件</div> -->
            <!-- <el-row :gutter="24"> -->
                <!-- <el-col :span="6" style="margin-top: 3px;">库位号<el-input v-model="inputData[0].stock" placeholder="请输入库位号"></el-input></el-col> -->
                <!-- <el-col :span="5" style="margin-top: 3px;">更改名称<el-input v-model="inputData[0].preName" placeholder="请输入预更改的名称"></el-input></el-col> -->
                <!-- <el-col :span="5" style="margin-top: 3px;">更改数量<el-input v-model="inputData[0].preNumber" placeholder="请输入预更改的数量"></el-input></el-col> -->
            <!-- </el-row> -->
        <!-- </div> -->
        <div class="table-box" >
            <div class="title">表格</div>
            <div>
                <el-table border :data="tableData" @selection-change="SelectRow" max-height="650px"> 
                    <el-table-column type="index"  fixed label="#" width="50px" />
                    <el-table-column type="selection" width="40px" fixed/>
                    <el-table-column show-overflow-tooltip label="箱号" width="100px" prop="boxNo"/>
                    <el-table-column show-overflow-tooltip label="sku" width="170px" prop="sku"/>
                    <el-table-column show-overflow-tooltip label="款号" width="330px" prop="shop_name"/>
                    <el-table-column show-overflow-tooltip label="数量" width="90px" prop="realNumber"/>
                    <el-table-column show-overflow-tooltip label="备注" width="140px" prop="remark"/>
                    <el-table-column show-overflow-tooltip label="尾箱" width="100px" prop="mantissa"/>
                    <el-table-column show-overflow-tooltip label="记录时间" width="188px" prop="dateTime"/>

                </el-table>
            </div>
        </div>
       
    </div>
</template>
<script setup>
    import {  ref,onMounted, compile } from 'vue';
    import { ElMessage } from 'element-plus';
    import axios from 'axios';
    import * as XLSX from 'xlsx';
    
    const inputData = ref([
        {
            // stock:'',
        }
    ]);
    const tableData = ref([
        {

        }
    ]);

    
    const getStorageList = () => {//查询
        console.log(inputData.value[0])
        axios.post('http://134.175.18.3:8083/api/HC/check',inputData.value[0],{
            headers: {'Content-Type': 'application/json;'}
        })
        .then(res=>{
        
           ElMessage.success('查询成功')
           console.log('响应数据:', res.data.data)
           tableData.value = res.data.data;

        })
        .catch(error => {
            console.log('错误状态码:', error.response?.status);
            console.log('错误信息:', error.response?.data);
            ElMessage.error(error.response?.data);
        })
    }
    
    const selectedRow = ref([]);
    const SelectRow = (selection) =>{
        selectedRow.value = selection.map(item => item);
        console.log(selectedRow.value)
    }
    const reset = () => {//重置
        inputData.value[0].stock = '';
        inputData.value[0].preName = '';
        inputData.value[0].preNumber = '';
    }

    const nextStep = () =>{//导出
        if(tableData.value.length === 0){
            ElMessage.error('没有数据可以导出');
            return;
        }
        
        // 创建导出数据，只包含表格中显示的字段
        const exportData = tableData.value.map(item => ({
            '箱号': item.boxNo,
            'sku': item.sku,
            '款号': item.shop_name,
            '数量': item.realNumber,
            '备注': item.remark,
            '记录时间': item.dateTime,
        }));
        
        const workbook = XLSX.utils.book_new();
        const worksheet = XLSX.utils.json_to_sheet(exportData);
        XLSX.utils.book_append_sheet(workbook, worksheet, '调拨数据');
        const date = new Date();
        const fileName = `调拨数据_${date.getFullYear()}${String(date.getMonth() + 1).padStart(2, '0')}${String(date.getDate()).padStart(2, '0')}.xlsx`;
        XLSX.writeFile(workbook, fileName);
        
        ElMessage.success('数据导出成功');
    };
    // const changeName = () =>{//改名
    //     if(selectedRow.value.length !== 1){
    //         ElMessage.error('请选择一条记录');
    //         return;
    //     }
    //     // console.log(selectedRow.value[0].logs_id)
    //     // console.log(inputData.value[0].preName)
    //     axios.post('http://134.175.18.3:8083/api/inventory/changeName',{
    //         logsId: selectedRow.value[0].logs_id, 
    //         preString: inputData.value[0].preName,
    //     },{headers: {'Content-Type': 'application/json'}})
    //     .then(res => {
    //         ElMessage.success('改名成功')
    //         getStorageList();
    //         // console.log('响应数据:', res.data)
    //     })
    //     .catch(err => {
    //         ElMessage.error('改名失败')
    //         console.log('错误状态码:', err.response?.status)
    //         console.log('错误信息:', err.response?.data)
    //     })
    // }
    // const changeNumber = () =>{//修改数量
    //     if(selectedRow.value.length !== 1){
    //         ElMessage.error('请选择一条记录');
    //         return;
    //     }
    //     // console.log(selectedRow.value[0].logs_id)
    //     // console.log(inputData.value[0].preName)
    //     axios.post('http://134.175.18.3:8083/api/inventory/changeNumber',{
    //         logsId: selectedRow.value[0].logs_id, 
    //         preString: inputData.value[0].preNumber,
    //     },{headers: {'Content-Type': 'application/json'}})
    //     .then(res => {
    //         ElMessage.success('修改数量成功')
    //         getStorageList();
    //         // console.log('响应数据:', res.data)
    //     })
    //     .catch(err => {
    //         ElMessage.error('修改数量失败')
    //         console.log('错误状态码:', err.response?.status)
    //         console.log('错误信息:', err.response?.data)
    //     })
    // }
    const delStorage = () =>{
        if(selectedRow.value.length < 1){
            ElMessage.error('请选择至少一条记录');
            return;
        }
        console.log(selectedRow.value)
        axios.post('http://134.175.18.3:8083/api/HC/delete',
            selectedRow.value[0]
        ,{
            headers: {'Content-Type': 'application/json'},
        })
        .then(res => {
            ElMessage.success('删除成功')
            getStorageList();
        })
        .catch(err => {
            ElMessage.error('删除失败')
            console.log('错误状态码:', err.response?.status)
            console.log('错误信息:', err.response?.data)    
        })
    }
    //整箱删除
    const delStorageAll = () =>{
        axios.get('http://134.175.18.3:8083/api/HC/deleteAll',{
            headers: {'Content-Type': 'application/json'},
        })
        .then(res => {
            ElMessage.success('整箱删除成功')
            getStorageList();
        })
        .catch(err => {
            ElMessage.error('整箱删除失败')
            console.log('错误状态码:', err.response?.status)
            console.log('错误信息:', err.response?.data)    
        })
    }
    //混箱删除
    const delStorageChaos = () =>{
        axios.get('http://134.175.18.3:8083/api/HC/deleteChaos',{
            headers: {'Content-Type': 'application/json'},
        })
        .then(res => {
            ElMessage.success('混箱删除成功')
            getStorageList();
        })
        .catch(err => {
            ElMessage.error('混箱删除失败')
            console.log('错误状态码:', err.response?.status)
            console.log('错误信息:', err.response?.data)    
        })
    }
</script>
<style scoped>
    .box {
        width: 62.2vw;
        height: 99.2%;
        padding:2px;
        border-radius: 10px;
        /* background-color: #3f72be; */
    }
    .check-box {
        padding: 0;
        margin: 5px 0;
        width: 99.9%;
        border-radius: 10px;
        border: 1px solid #849aa7;
        background-color: #e5e8ee;

    }
    .oprate-box {
        padding: 0;
        /* margin-bottom: 5px; */
        width: 99.9%;
        border-radius: 10px;
        border: 1px solid #849aa7;
        background-color: #e5e8ee;
    }
    .table-box {
        padding: 0;
        margin-top: 5px;
        width: 99.9%;
        height: 60%;
        border-radius: 10px;
        border: 1px solid #849aa7;
        background-color: #e5e8ee;
    }
    .title{
        padding-left: 10px;
        padding-top: 3px;
        padding-bottom: 5px;
        font-size: 18px;
    }
    .el-input{
        margin-left: 5px;
        margin-right: 20px;
        width: 50%;
        height: 25px;
    }
    .el-row{
        padding-right:15px ;
        margin-bottom: 10px;
        font-size: 14px;
    }
    .el-col{
        margin-left: 15px;
        display: flex;
        justify-content: end;
    }
    .el-button{
        margin-left: 8px;
        margin-bottom: 8px;
        /* width: 50px; */
        height: 25px;
        font-size: 13px;
    }
    .el-table .el-table__header-wrapper th {
        background-color: #d5dbe4; 
        color: #da1a1a; 
        font-size: 16px;
        font-weight: bold; 
        padding: 10px 0; 
    }
    .sector{
        font-size: 26px;
        float: right;
        margin-right: 10px;
    }
     .el-icon{
        font-size: 17px;
    }
    .el-icon:hover{
        color: #cfc5c5;
    }

    .img-box{
        width: 100%;
        height: 75%;
        overflow: auto;
        text-align: center;
        margin-top: 10px;
        /* background-color: #da1a1a; */
    }
    img{
        max-width: 80%;
        height: auto;
    }
    .close-photo{
        height: 30px;
        position: absolute;
        top: 180px;
        right: 100px;
        font-size: 20px;
        color: #575454;
    }
</style>