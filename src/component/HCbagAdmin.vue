<template>
    <div class="box">
        <div class="oprate-box">
            <div class="title">操作<span class="sector">好春回收袋登记后台系统</span></div>
            <div>
                <el-button @click="getStorageList">查询</el-button>
                <!-- <el-button @click="reset()">重置</el-button> -->
                <el-button @click="nextStep()">导出</el-button>
            </div>
        </div>
       <div class="check-box">
            <div class="title">查询条件</div>
            <el-row :gutter="24">
                <el-col :span="8">日期范围
                    <el-date-picker
                    v-model="rangeDate"
                    type="datetimerange"
                    range-separator="--"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"
                    value-format="YYYY-MM-DD HH:mm:ss"
                    /> 
                </el-col>
            </el-row>
        </div>
        <div class="table-box" >
            <div class="title">表格</div>
            <div>
                <el-table border :data="tableData" @selection-change="SelectRow" max-height="650px"> 
                    <el-table-column type="index"  fixed label="#" width="50px" />
                    <el-table-column type="selection" width="40px" fixed/>
                    <el-table-column show-overflow-tooltip label="提交人员" width="110px" prop="operator" fixed/>
                    <el-table-column show-overflow-tooltip label="22*32" width="106px" prop="bag2232"/>
                    <el-table-column show-overflow-tooltip label="28*38" width="106px" prop="bag2838"/>
                    <el-table-column show-overflow-tooltip label="34*43" width="106px" prop="bag3443"/>
                    <el-table-column show-overflow-tooltip label="35*45" width="106px" prop="bag3545"/>
                    <el-table-column show-overflow-tooltip label="35*25" width="106px" prop="bag3525"/>
                    <el-table-column show-overflow-tooltip label="30*38" width="106px" prop="bag3038"/>
                    <el-table-column show-overflow-tooltip label="45*65" width="106px" prop="bag4565"/>
                    <el-table-column show-overflow-tooltip label="超大/特大" width="106px" prop="bagBig"/>
                    <el-table-column show-overflow-tooltip label="提交时间" width="161px" prop="dateTime"/>
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
    
    const inputData = ref(
        {
            // sku:'',
            dateTime:'',
            backTime:''
        }
    );
    const tableData = ref([
        {
        }
    ]);
    const rangeDate = ref([])


    const getStorageList = () => {//详细查询
        if(rangeDate.value.length !== 0){
            inputData.value.dateTime = rangeDate.value[0],
            inputData.value.backTime = rangeDate.value[1]
            // console.log(inputData.value)
        }

        axios.post('http://134.175.18.3:8083/api/HC/bagCheck',inputData.value,{
            headers: {'Content-Type': 'application/json;'}
        })
        .then(res=>{
           ElMessage.success('查询成功')
           tableData.value = res.data.data;
        })
        .catch(error => {
            tableData.value = []
            // console.log('错误状态码:', error.response?.status);
            // console.log('错误信息:', error.response?.data);
            ElMessage.warning(error.response?.data);
        })
    }
    
    const selectedRow = ref([]);
    const SelectRow = (selection) =>{
        selectedRow.value = selection.map(item => item);
        // console.log(selectedRow.value)
    }
    // const reset = () => {//重置
    //     inputData.value[0].action = '';
    // }

    const nextStep = () =>{//导出
        if(tableData.value.length === 0){
            ElMessage.error('没有数据可以导出');
            return;
        }
        
        // 创建导出数据，只包含表格中显示的字段
        const exportData = tableData.value.map(item => ({
            '取走人':item.operator,
            '22*32':item.bag2232,
            '28*38':item.bag2838,
            '34*43':item.bag3443,
            '35*45':item.bag3545,
            '35*25':item.bag3525,
            '30*38':item.bag3038,
            '45*65':item.bag4565,
            '超大/特大':item.bagBig,
            '取走时间':item.dateTime,
        }));
        
        const workbook = XLSX.utils.book_new();
        const worksheet = XLSX.utils.json_to_sheet(exportData);
        XLSX.utils.book_append_sheet(workbook, worksheet, '好春回收袋子统计');
        const date = new Date();
        const fileName = `好春回收袋子数据_${date.getFullYear()}${String(date.getMonth() + 1).padStart(2, '0')}${String(date.getDate()).padStart(2, '0')}.xlsx`;
        XLSX.writeFile(workbook, fileName);
        ElMessage.success('数据导出成功');
    };
    
   
   
</script>
<style scoped>
    .box {
        width: 76vw;
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
        margin-left: 15px;
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