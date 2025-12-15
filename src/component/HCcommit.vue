<template>
    <div class="box">
        <h3>调拨登记提交</h3>
        <span>请扫描SKU</span>
        <el-input placeholder="请扫描SKU" v-model="commitData.sku" @change="focusSysInput" ></el-input>
        <span>请输入箱号</span>
        <el-input  v-model="commitData.boxNo" @input="validate(commitData.boxNo)" placeholder="请输入箱号"></el-input>
        
        <span>请输入数量</span>
        <el-input ref="sysInput" placeholder="请输入数量" v-model="commitData.realNumber" @input="validateSys(commitData.realNumber)"></el-input>
        <span>请输入登记人员</span>
        <el-input placeholder="请输入登记人员" v-model="commitData.operator" ></el-input>
        <span>请输入备注</span>
        <el-input ref="remarkInput" placeholder="请输入备注（选填）" v-model="commitData.remark" ></el-input>
        <span>是否尾箱</span>
        <el-radio-group v-model="commitData.mantissa">
            <el-radio value="是" size="large">是</el-radio>
            <el-radio value="否" size="large">否</el-radio>
        </el-radio-group>
        
        <el-button type="primary" @click.prevent="commit" :loading="loading" :disabled="loading">提交</el-button>
    </div>
</template>
<script setup>
    import { ref, computed } from 'vue'
    import { ElMessage } from 'element-plus'
    import axios from 'axios'

    const commitData = ref({
        operator: localStorage.getItem('HCoperator') || '',
        sku: '',
        boxNo: '',
        realNumber: '',
        remark: '',
        dateTime:'',
        mantissa: '否',
        complete:'否'
    })
    
    // 添加加载状态，防止重复提交
     const loading = ref(false)
     
     // 记录最后一次点击时间，用于节流
     const lastClickTime = ref(0)
     
     // 定义节流时间间隔（1秒）
     const throttleTime = 1000
    
    const sysInput = ref(null)
    const realInput = ref(null)
    const remarkInput = ref(null)
    
    const focusSysInput = () => {
        sysInput.value.focus()
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
        // if (loading.value) return; 
        // loading.value = true;
        // // 获取当前时间
        // const currentTime = Date.now()
        
        // // 检查是否在节流时间内
        // if (currentTime - lastClickTime.value < throttleTime) {
        //     ElMessage.warning('操作过于频繁，请稍后再试')
        //     return
        // }
        
        
        // 更新最后点击时间
        // lastClickTime.value = currentTime
        
        localStorage.setItem('HCoperator', commitData.value.operator)
        if(commitData.value.operator === '' || commitData.value.boxNo === '' || commitData.value.sku === '' || commitData.value.realNumber === ''){
            ElMessage.error('请完整填写信息')
            // 提交失败，结束加载状态
            loading.value = false
            return
        }
        
        commitData.value.dateTime = getDate();
        // 开始加载，禁用按钮
        loading.value = true
        
        axios.post('http://134.175.18.3:8083/api/HC/commit', commitData.value, {headers: {'Content-Type': 'application/json'}})
        .then(res => {
            ElMessage.success('提交成功')
            commitData.value.sku = ''
            if(commitData.value.mantissa === '否'){
                const numberPart = computed(() => {
                    return commitData.value.boxNo.replace(/\D/g, '')
                })
                // 提取字母部分
                const letterPart = computed(() => {
                    return commitData.value.boxNo.replace(/[^A-Za-z]/g, '')
                })
                // console.log(letterPart.value, numberPart.value)
                let NumLength = numberPart.value.length
                let NUM = parseInt(numberPart.value,10)+1
                // console.log("加完后："+NUM)
                let newNum = NUM.toString().padStart(NumLength, '0')
                // console.log("最后："+newNum)
                commitData.value.boxNo = letterPart.value + newNum
            }
            
        })
        .catch(err => {
            ElMessage.error('提交失败')
            console.log(err)
        })
        loading.value = false
    }

    const validate = (number) =>{
        commitData.value.boxNo = number.trim()
    }
    const validateSys = (number) =>{
        commitData.value.realNumber = number.replace(/[^\d,-]/g, '')
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
