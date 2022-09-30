<script setup lang="ts">
  import {
    Plus,
    Delete
  } from '@element-plus/icons-vue'
  import { reactive, } from 'vue'

  import {
     evaluate,  round,
  } from 'mathjs'

  import { v4 as uuidv4 } from 'uuid';

  interface PriceData{
    offset: number,
    price: number,
    afterProfit: number,
    afterOffet: number,
    id: any
  }


  const dataTemp:PriceData ={
    offset: -5,
    price: 1000,
    afterProfit: 0,
    afterOffet: 0,
    id: 0,
  }

  const dataArray = reactive([{...dataTemp,id:uuidv4()}])

  const hadnleAdd = function (){
    dataArray.push({...dataTemp,id:uuidv4()})
  }

  const hadnleDel = function (index:number){
      dataArray.splice(index,1)
  }


  const handleCal = function (){
    let count1:number = 0 // 总资金
    let count2:number = 0 // 投入后，当前剩余资金
    dataArray.map((data:PriceData,index:number,arr)=>{
      count1 = evaluate(`${count1} + ${data.price*1}`) //
      count2 = evaluate(`${count2} * ${(1+data.offset/100)} + ${data.price}`)  // 目前剩余资金

      // console.debug( count1,count2)

      // 盈亏金额 = 之前盈亏+本次盈亏【上次购买金额*本次调整幅度】

      data.afterProfit = evaluate(`${count2} - ${count1}`)
      // 盈亏幅度 = 盈亏金额/(本次购买金额 + 之前总金额)

      data.afterOffet = evaluate(`(${data.afterProfit}/${count1})*100`)

      data.afterOffet = round(data.afterOffet,2)


      return data
    })

    console.debug(dataArray)
  }




</script>

<template>
  <h1>盈亏计算</h1>
  <el-form
      :inline="true"
    label-width="120px"
    style="max-width: 800px"
  >

    <section v-for="(data,index) in dataArray" :key="data.id">
      <el-form-item label="调整幅度">
        <el-input v-model="data.offset" type="number">
          <template #append>%</template>
        </el-input>
      </el-form-item>
      <el-form-item label="成交价格">
        <el-input v-model="data.price" type="number"/>
      </el-form-item>
      <el-form-item label="买入后盈亏金额">
        <el-input v-model="data.afterProfit" type="number"/>
      </el-form-item>
      <el-form-item label="买入后盈亏幅度">
        <el-input v-model="data.afterOffet" type="number">
          <template #append>%</template>
        </el-input>
      </el-form-item>
      <div class="delBtn"><el-button type="danger" :icon="Delete" circle @click="hadnleDel(index)"/></div>
    </section>
    <div class="addBtn">
      <el-button type="danger" :icon="Plus" circle @click="hadnleAdd"/>
    </div>

    <div class="addBtn">
      <el-button type="warning" @click="handleCal">开始计算</el-button>
    </div>

  </el-form>
</template>

<style>

section{
  position: relative;
  padding: 20px 0;
  border: 1px dashed red;
}
.addBtn{
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
.delBtn{
  position: absolute;
  right: 20px;
  top: 50px;
}
</style>
