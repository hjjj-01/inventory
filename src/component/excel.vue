<template>
    <div class="box">
        <div class="oprate-box">
            <div class="title">操作</div>
            <div>
                <el-button @click="nextStep()">导出</el-button>
            </div>
            
        </div>
       <div class="table-box">
            <div class="title">库位范围</div>1
            <div>楼层号：<el-input v-model="floor" placeholder="请输入楼层"></el-input> ---- 2或者3</div>
            <div>区域范围：<el-input v-model="region" placeholder="请输入区域"></el-input>-<el-input v-model="region1" placeholder="请输入区域"></el-input></div>
            <div>通道范围：<el-input v-model="path" placeholder="请输入通道"></el-input>-<el-input v-model="path1" placeholder="请输入通道"></el-input></div>
            <div>库位范围：<el-input v-model="storage" placeholder="请输入库位"></el-input>-<el-input v-model="storage1" placeholder="请输入库位"></el-input></div>
            <div>架位范围：<el-input v-model="shelf" placeholder="请输入架位"></el-input>-<el-input v-model="shelf1" placeholder="请输入架位"></el-input></div>
       </div>
    </div>
</template>
<script setup>
    import {  ref,onMounted, compile } from 'vue';
    import { ElMessage } from 'element-plus';
    import * as XLSX from 'xlsx';
     const floor = ref("");
    const region = ref("");
    const path = ref("");
    const storage = ref("");
    const shelf = ref("");
    const region1 = ref("");
    const path1 = ref("");
    const storage1 = ref("");
    const shelf1 = ref("");
    
    const nextStep = () => {
    const exportData1 = [];
    for (let i = 44; i <= 52; i += 2) {       
        for (let j = 1; j <= 48; j += 1) {      
            const J = String(j).padStart(2, '0');
            const I = String(i).padStart(2, '0');
            // for (let k = 1; k <= 4; k++) {  
                exportData1.push({ 编号: `W3-C2-${I}-${J}` });
                // exportData1.push({ 编号: `W3-C2-${I}-${J}-${k}` });
            // }
        }
    }
    // for (let i = 12; i <= 50; i += 2) {       
    //     for (let j = 1; j <= 40; j += 1) {      
    //         const J = String(j).padStart(2, '0');
    //         const I = String(i).padStart(2, '0');
    //         for (let k = 1; k <= 4; k++) {  
    //             // exportData1.push({ 编号: `W3-C1-${I}-${J}` });
    //             exportData1.push({ 编号: `W3-D1-${I}-${J}-${k}` });
    //         }
    //     }
    // }
    // for (let i = 43; i <= 51; i += 2) {       
    //     for (let j = 1; j <= 80; j += 1) {      
    //         const J = String(j).padStart(2, '0');
    //         const I = String(i).padStart(2, '0');
    //         for (let k = 1; k <= 4; k++) {  
    //             // exportData1.push({ 编号: `W3-D2-${I}-${J}` });
    //             exportData1.push({ 编号: `W3-D2-${I}-${J}-${k}` });
    //         }
    //     }
    // }
    //  for (let i = 53; i <= 79; i += 2) {       
    //     for (let j = 1; j <= 72; j += 1) {      
    //         const J = String(j).padStart(2, '0');
    //         const I = String(i).padStart(2, '0');
    //         for (let k = 1; k <= 4; k++) {  
    //             // exportData1.push({ 编号: `W3-D2-${I}-${J}` });
    //             exportData1.push({ 编号: `W3-D2-${I}-${J}-${k}` });
    //         }
    //     }
    // }
    // for (let i = 44; i <= 68; i += 2) {       
    //     for (let j = 1; j <= 40; j += 1) {      
    //         const J = String(j).padStart(2, '0');
    //         const I = String(i).padStart(2, '0');
    //         for (let k = 1; k <= 4; k++) {  
    //             // exportData1.push({ 编号: `W3-D2-${I}-${J}` });
    //             exportData1.push({ 编号: `W3-D2-${I}-${J}-${k}` });
    //         }
    //     }
    // }

    const workbook = XLSX.utils.book_new();
    const worksheet = XLSX.utils.json_to_sheet(exportData1);
    XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1');

    const date = new Date();
    const fileName = `一.xlsx`;
    XLSX.writeFile(workbook, fileName);

    ElMessage.success('数据导出成功');
};

    
</script>
<style scoped>
    .box {
        width: 50.2vw;
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
        width: 65px;
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