<template>
    <div class="box">
        <div class="oprate-box">
            <div class="title">操作<span class="sector">好春PDA登记后台系统</span></div>
            <div>
                <el-button @click="getStorageList">查询</el-button>
                <el-button @click="reset()">重置</el-button>
                <el-button @click="nextStep()">导出</el-button>
            </div>
        </div>
       <div class="check-box">
            <div class="title">查询条件</div>
            <el-row :gutter="24">
                <el-col :span="4" style="margin-top: 3px;">状态
                    <el-select  placeholder="请选择PDA状态" v-model="inputData[0].action">
                        <el-option v-for="item in actionList" :key="item.value" :label="item.label" :value="item.value"></el-option>
                    </el-select>
                </el-col>
            </el-row>
        </div>
        <div class="table-box" >
            <div class="title">表格</div>
            <div>
                <el-table border :data="tableData" @selection-change="SelectRow" max-height="650px"> 
                    <el-table-column type="index"  fixed label="#" width="50px" />
                    <el-table-column type="selection" width="40px" fixed/>
                    <el-table-column show-overflow-tooltip label="PDA编号" width="110px" prop="pdaNo" fixed/>
                    <el-table-column show-overflow-tooltip label="取走人" width="126px" prop="operator"/>
                    <el-table-column show-overflow-tooltip label="取走时间" width="255px" prop="dateTime"/>
                    <el-table-column show-overflow-tooltip label="放回人" width="129px" prop="remark"/>
                    <el-table-column show-overflow-tooltip label="放回时间" width="225px" prop="backTime"/>
                    <el-table-column show-overflow-tooltip label="目前状态" width="100px" prop="action"/>
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
            action:'',
        }
    ]);
    const tableData = ref([
        {
            operator:'',
            pdaNo:'',
            action:'',
            dateTime:'',
            backTime:'',
            remark:'',//返回人
        }
    ]);
    const actionList = ref([
        {
            value:"放回",
            lable:"放回"
        },
        {
            value:"取走",
            label:"取走"
        }
    ])


    const getStorageList = () => {//查询
        console.log(inputData.value[0])
        axios.post('http://134.175.18.3:8083/api/HC/pdaCheck',inputData.value[0],{
            headers: {'Content-Type': 'application/json;'}
        })
        .then(res=>{
        
           ElMessage.success('查询成功')
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
        // console.log(selectedRow.value)
    }
    const reset = () => {//重置
        inputData.value[0].action = '';
    }

    const nextStep = () =>{//导出
        if(tableData.value.length === 0){
            ElMessage.error('没有数据可以导出');
            return;
        }
        
        // 创建导出数据，只包含表格中显示的字段
        const exportData = tableData.value.map(item => ({
            'PDA编号':item.pdaNo,
            '取走人':item.operator,
            '取走时间':item.dateTime,
            '放回人':item.remark,
            '放回时间':item.backTime,
            '当前状态':item.action
        }));
        
        const workbook = XLSX.utils.book_new();
        const worksheet = XLSX.utils.json_to_sheet(exportData);
        XLSX.utils.book_append_sheet(workbook, worksheet, 'PDA');
        const date = new Date();
        const fileName = `PDA数据_${date.getFullYear()}${String(date.getMonth() + 1).padStart(2, '0')}${String(date.getDate()).padStart(2, '0')}.xlsx`;
        XLSX.writeFile(workbook, fileName);
        ElMessage.success('数据导出成功');
    };
    
   
   
</script>
<style scoped>
    .box {
        width: 65.1vw;
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