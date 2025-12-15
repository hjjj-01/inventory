<template>
    <div class="box">
        <!-- <h3>库位多货提交</h3> -->
        <el-button type="primary" @click="toCheck">查询</el-button>
        <span></span>
        <el-button type="primary" @click="toadd">上架</el-button>
        <span></span>
        <el-button type="primary" @click="tosub">下架</el-button>
       
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    import { ElMessage } from 'element-plus'
    import { useRoute } from 'vue-router'
    const route = useRoute()
    import router from '@/router'

    const toCheck = () => {
       router.push('/YCcheck')
    }

    const toadd = () => {
        router.push('/YCadd')
    }
    const tosub = () => {
        router.push('/YCsub')
    }
   
</script>

<style scoped>
    .box{
        width: 300px;
        height: 400px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        background-color: rgb(224, 238, 233);
    }
    
    .el-button{
        width: 100px;
        margin-top: 10px;
        margin-bottom: 3px;
    }
</style>
