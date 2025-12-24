<template>
    <div class="page">
      <el-card class="card" shadow="hover">
        <h2 class="title">回收袋子登记系统</h2>
  
        <el-form label-position="top">
          <el-form-item label="登记人员">
            <el-input
              placeholder="请输入登记人员"
              v-model="commitData.operator"
              clearable
            />
          </el-form-item>
  
          <el-form-item label="22*32">
            <el-input
              placeholder="请输入22*32的数量"
              v-model="commitData.bag2232"
              @input="filterNumber('bag2232')"
              clearable
            />
          </el-form-item>
          <el-form-item label="28*38">
            <el-input
              placeholder="请输入28*38的数量"
              v-model="commitData.bag2838"
              @input="filterNumber('bag2838')"
              clearable
            />
          </el-form-item>
          <el-form-item label="34*43">
            <el-input
              placeholder="请输入34*43的数量"
              v-model="commitData.bag3443"
              @input="filterNumber('bag3443')"
              clearable
            />
          </el-form-item>
          <el-form-item label="35*45">
            <el-input
              placeholder="请输入35*45的数量"
              v-model="commitData.bag3545"
              @input="filterNumber('bag3545')"
              clearable
            />
          </el-form-item>
          <el-form-item label="35*25">
            <el-input
              placeholder="请输入35*25的数量"
              v-model="commitData.bag3525"
              @input="filterNumber('bag3525')"
              clearable
            />
          </el-form-item>
          <el-form-item label="30*38">
            <el-input
              placeholder="请输入30*38的数量"
              v-model="commitData.bag3038"
              @input="filterNumber('bag3038')"
              clearable
            />
          </el-form-item>
          <el-form-item label="45*65">
            <el-input
              placeholder="请输入45*65的数量"
              v-model="commitData.bag4565"
              @input="filterNumber('bag4565')"
              clearable
            />
          </el-form-item>
          <el-form-item label="特大">
            <el-input
              placeholder="请输入特大/超大的数量"
              v-model="commitData.bagBig"
              @input="filterNumber('bagBig')"
              clearable
            />
          </el-form-item>
          
          <el-button
              type="primary"
              size="large"
              class="submit-btn"
              @click="commit"
            >
              提交登记
            </el-button>
        </el-form>
      </el-card>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { ElMessage } from 'element-plus'
  import axios from 'axios'
  
  const commitData = ref({
    operator: localStorage.getItem('HcBagOperator') || '',
    bag2232:'',
    bag2838:'',
    bag3443:'',
    bag3545:'',
    bag3525:'',
    bag3038:'',
    bag4565:'',
    bag4670:'',
    bagBig:''
  })
  
  const getDate = () => {
    const date = new Date()
    const year = date.getFullYear()
    const month = date.getMonth() + 1
    const day = date.getDate()
    return `${year}-${month}-${day}`
  }
  
  const commit = () => {
    localStorage.setItem('HcBagOperator', commitData.value.operator)
  
    if (
      commitData.value.operator === '') {
      ElMessage.error('请填写登记人')
      return
    }
  
    commitData.value.dateTime = getDate()
  
    axios
      .post(
        'http://134.175.18.3:8083/api/HC/BagCommit',
        commitData.value,
        { headers: { 'Content-Type': 'application/json' } }
      )
      .then(() => {
        ElMessage.success('提交成功')
      })
      .catch(() => {
        ElMessage.error('提交失败')
      })
  }
  const filterNumber = (key) => {
    commitData.value[key] = commitData.value[key]
      ?.toString()
      .replace(/\D/g, '')
  }

  </script>
  
  <style scoped>
  .page {
    min-height: 100vh;
    background: linear-gradient(135deg, #e8f3f1, #f5fdfb);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .card {
    width: 95vw;
    max-width: 90%;
    border-radius: 16px;
    padding: 20px 24px;
  }
  
  .title {
    text-align: center;
    margin-bottom: 24px;
    font-weight: 600;
    color: #2c3e50;
  }
  
  .radio-group {
    display: flex;
    justify-content: center;
    width: 100%;
  }
  
  .submit-btn {
    width: 100%;
    border-radius: 12px;
    font-size: 16px;
    letter-spacing: 2px;
  }
  </style>
  