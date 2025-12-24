<template>
    <div class="page">
      <el-card class="card" shadow="hover">
        <h2 class="title">PDA 登记系统</h2>
  
        <el-form label-position="top">
          <el-form-item label="登记人员">
            <el-input
              placeholder="请输入登记人员"
              v-model="commitData.operator"
              clearable
            />
          </el-form-item>
  
          <el-form-item label="PDA 编号">
            <el-input
              placeholder="请输入 PDA 编号"
              v-model="commitData.pdaNo"
              clearable
            />
          </el-form-item>
  
          <el-form-item label="操作类型">
            <el-radio-group v-model="commitData.action" class="radio-group">
              <el-radio-button value="取走">取走</el-radio-button>
              <el-radio-button value="放回">放回</el-radio-button>
            </el-radio-group>
          </el-form-item>
  
          <el-form-item>
            <el-button
              type="primary"
              size="large"
              class="submit-btn"
              @click="commit"
            >
              提交登记
            </el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { ElMessage } from 'element-plus'
  import axios from 'axios'
  
  const commitData = ref({
    operator: localStorage.getItem('HcPdaOperator') || '',
    pdaNo: '',
    dateTime: '',
    action: '',
    complete: '未完成'
  })
  
  const getDate = () => {
    const date = new Date()
    const year = date.getFullYear()
    const month = date.getMonth() + 1
    const day = date.getDate()
    const hour = date.getHours()
    const minute = date.getMinutes()
    const second = date.getSeconds()
    return `${year}-${month}-${day} ${hour}:${minute}:${second}`
  }
  
  const commit = () => {
    localStorage.setItem('HcPdaOperator', commitData.value.operator)
  
    if (
      commitData.value.operator === '' ||
      commitData.value.pdaNo === '' ||
      commitData.value.action === ''
    ) {
      ElMessage.error('请完整填写信息')
      return
    }
  
    commitData.value.dateTime = getDate()
  
    axios
      .post(
        'http://134.175.18.3:8083/api/HC/PdaCommit',
        commitData.value,
        { headers: { 'Content-Type': 'application/json' } }
      )
      .then(() => {
        ElMessage.success('提交成功')
      })
      .catch(() => {
        if (commitData.value.action === '取走') {
          ElMessage.error('此编号今天已被取走')
        } else {
          ElMessage.error('此编号今天已被返回')
        }
      })
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
  