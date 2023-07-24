<script setup lang="ts">

import {onBeforeUnmount, reactive, ref, watch} from "vue"

const isStart = ref(false)

const shortcuts = [
  {
    text: 'after two day',
    value: () => {
      const date = new Date()
      date.setTime(date.getTime() + 3600 * 1000 * 24 * 2)
      return date
    },
  },
]

const disabledDate = (time: Date) => {
  return time.getTime() > (Date.now() + 3600 * 1000 * 24 * 7) ||
    time.getTime() < (Date.now() - 3600 * 1000 * 24 * 7)
}

const date = ref(shortcuts[0].value())

const timeFragments = [
  {
    label: 1075722411,
    value: "08:30-10:29"
  },
  {
    label: 1101041060,
    value: "10:30-12:00"
  },
  {
    label: 1030534428,
    value: "12:30-14:29"
  },
  {
    label: 1075722410,
    value: "14:30-16:30"
  },
]

const timePart = ref(0)

const quantity = ref(5)
const interval = ref(1000)

const scrollbarRef = ref()
const innerRef = ref()
const logs = reactive([`${new Date().toLocaleString()} init`])

watch(logs, () => {
  // console.log(scrollbarRef.value)
  // console.log(innerRef.value!.clientHeight)
  if (innerRef.value!.clientHeight > 400) {
    scrollbarRef.value!.setScrollTop(innerRef.value!.clientHeight)
  }
}, {
  flush: 'post'
})

let timer: NodeJS.Timer
const handleChange = () => {
  if (isStart.value) {
    clearInterval(timer)
    timer = setInterval(() => {
      console.log(new Date().toLocaleString(), 'hi')
      if (logs.length <= 100) {
        logs.push(`${new Date().toLocaleString()} hi`)
      } else {
        logs.shift()
        logs.push(`${new Date().toLocaleString()} hi`)
      }
    }, interval.value)
  } else {
    console.log('stop')
    clearInterval(timer)
  }
}

onBeforeUnmount(() => {
  console.log('unmount')
  clearInterval(timer)
})

</script>

<template>


    <el-row class="row-bg" justify="center">
        <el-col :span="6">
            <el-switch
                    v-model="isStart"
                    size="large"
                    inline-prompt
                    active-text="开始"
                    inactive-text="停止"
                    @change="handleChange"
                    style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"
            />
        </el-col>
    </el-row>
    <el-divider/>
    <el-row :gutter="20">
      <span class="ml-3 w-35 text-gray-600 inline-flex items-center"
      >Using attributes</span
      >
        <div>
            <el-date-picker
                    v-model="date"
                    class="w-50 m-2"
                    type="date"
                    placeholder="Pick a day"
                    :disabled-date="disabledDate"
                    :shortcuts="shortcuts"
                    :size="'default'"
            />
        </div>


        <el-input-number v-model="quantity" :min="1" :max="10" controls-position="right" label="票数"/>


        <el-input v-model="interval" placeholder="Please input">
            <template #prepend>间隔</template>
            <template #append>ms</template>
        </el-input>

    </el-row>

    <el-row class="row-bg" justify="center">
        <el-col :span="20">
            <el-radio-group v-model="timePart">
                <el-radio-button v-for="times in timeFragments" :label="times.label">{{ times.value }}</el-radio-button>
            </el-radio-group>
        </el-col>
    </el-row>
    <el-divider/>
    <el-card class="box-card">
        <el-scrollbar height="400px" ref="scrollbarRef">
            <div ref="innerRef">
                <div v-for="o in logs" :key="o">{{ o }}</div>
            </div>

        </el-scrollbar>
    </el-card>
</template>

<style scoped>
.el-row {
    margin-bottom: 20px;
}
</style>
