<template>
    <div class="box">
        <div class="oprate-box">
            <div class="title">操作<span class="sector">异常箱后台系统</span></div>
            <div>
                <el-button @click="getYcBoxList">查询整体</el-button>
                <el-button @click="getYcBoxDelList">查询详情</el-button>
                <el-button @click="reset()">重置</el-button>
                <!-- <el-button @click="changeName()">修改名称</el-button> -->
                <!-- <el-button @click="changeNumber()">修改数量</el-button> -->
                <!-- <el-button @click="delStorage()">删除</el-button> -->
                <el-button @click="nextStep()">导出</el-button>
            </div>
        </div>
       <div class="check-box">
            <div class="title">查询条件</div>
            <el-row :gutter="24">
                <el-col :span="5" style="margin-top: 3px;">箱号<el-input v-model="inputData[0].boxNo" placeholder="请输入箱号"></el-input></el-col>
                <el-col :span="5" style="margin-top: 3px;">SKU号<el-input v-model="inputData[0].sku" placeholder="请输入SKU号"></el-input></el-col>
                <el-col :span="5" style="margin-top: 3px;">款号<el-input v-model="inputData[0].shop_name" placeholder="请输入商品名称"></el-input></el-col>
            </el-row>
        </div>
        <div class="table-box" >
            <div v-if="table1Show">
            <div class="title">整体报表</div>
            <el-table-v2
                :columns="columns"
                :data="tableData"
                :width="1200"
                :height="600"
            />
                
            </div>
            <div v-if="table2Show">
                <div class="title">详情报表</div>
                <el-table-v2
                    :columns="columns2"
                    :data="tableData2"
                    :width="1200"
                    :height="600"
                />
                
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
            sku:'',
            boxNo:'',
            shop_name:'',
            // preName:'',
            // preNumber:'',
        }
    ]);
    const tableData = ref([]);
    const tableData2 = ref([]);
    const table1Show = ref(true);
    const table2Show = ref(false);
    const columns = [
        {
            key: 'index',
            title: '#',
            width: 80,
            cellRenderer: ({ rowIndex }) => rowIndex + 1
        },
        {
            key: 'boxNo',
            dataKey: 'boxNo',
            title: '箱号',
            width: 140,
        },
        {
            key: 'sku',
            dataKey: 'sku',
            title: 'SKU号',
            width: 180,
        },
        {
            key: 'shop_name',
            dataKey: 'shop_name',
            title: '商品名称',
            width: 356,
        },
        {
            key: 'realNumber',
            dataKey: 'realNumber',
            title: '数量',
            width: 90,
        }
        ]
    const columns2 = [
        {
            key: 'index',
            title: '#',
            width: 80,
            cellRenderer: ({ rowIndex }) => rowIndex + 1
        },
        {
            key: 'boxNo',
            dataKey: 'boxNo',
            title: '箱号',
            width: 140,
        },
        {
            key: 'sku',
            dataKey: 'sku',
            title: 'SKU号',
            width: 180,
        },
        {
            key: 'shop_name',
            dataKey: 'shop_name',
            title: '商品名称',
            width: 356,
        },
        {
            key: 'realNumber',
            dataKey: 'realNumber',
            title: '数量',
            width: 90,
        },
        {
            key: 'dateTime',
            dataKey: 'dateTime',
            title: '操作时间',
            width: 180,
        }
    ]
    
    
    const getYcBoxList = () => {//查询
        // console.log(inputData.value[0])
        axios.post('http://134.175.18.3:8083/api/inventory/YcBoxCheck',inputData.value[0],{
            headers: {'Content-Type': 'application/json;'}
        })
        .then(res=>{
            console.log(res.data.data)
            ElMessage.success('查询成功')
            table1Show.value = true;
            table2Show.value = false;
            tableData.value = res.data.data;

        })
        .catch(error => {
            console.log('错误状态码:', error.response?.status);
            console.log('错误信息:', error.response?.data);
            ElMessage.error(error.response?.data);
        })
    }
    const getYcBoxDelList = () => {//查询详情
        console.log(inputData.value[0])
        axios.post('http://134.175.18.3:8083/api/inventory/YcBoxCheckDel',inputData.value[0],{
            headers: {'Content-Type': 'application/json;'}
        })
        .then(res=>{
            console.log(res.data.data)
            ElMessage.success('查询成功')
            tableData2.value = res.data.data;
            table1Show.value = false;
            table2Show.value = true;
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
        // console.log(selectedRow.value)
    }
    const reset = () => {//重置
        inputData.value[0].sku = '';
        inputData.value[0].shop_name = '';
        inputData.value[0].boxNo = '';
    }

    const nextStep = () =>{//导出
        const exportData = [];
        // 创建导出数据，只包含表格中显示的字段
        if(table1Show.value){
            exportData.value = tableData.value.map(item => ({
                '箱号': item.boxNo,
                'SKU号': item.sku,
                '商品名称': item.shop_name,
                '数量': item.realNumber
            }));
        }else if(table2Show.value){
            exportData.value = tableData2.value.map(item => ({
                '箱号': item.boxNo,
                'SKU号': item.sku,
                '商品名称': item.shop_name,
                '数量': item.realNumber,
                '操作时间': item.dateTime
            }));
        }
        
        const workbook = XLSX.utils.book_new();
        const worksheet = XLSX.utils.json_to_sheet(exportData.value);
        XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1');
        const date = new Date();
        const fileName = `异常箱数据_${date.getFullYear()}${String(date.getMonth() + 1).padStart(2, '0')}${String(date.getDate()).padStart(2, '0')}.xlsx`;
        XLSX.writeFile(workbook, fileName);
        
        ElMessage.success('数据导出成功');
    };



</script>
<style scoped>
    .box {
        width: 60.2vw;
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
        width: 60%;
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