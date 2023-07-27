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
  // console.log(innerRef.value!.clientHeight)
  // console.log(scrollbarRef.value)
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
      if (logs.length > 100) {
        logs.shift()
      }
      logs.push(`${new Date().toLocaleString()} ${Math.random()}`)
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
    <el-container id="box">
        <el-header class="head">
            <el-row class="row-bg" justify="space-between">
                <el-col :span="6">
                    <p class="title">Museum</p>
                </el-col>
            </el-row>
        </el-header>
        <el-main style="height: auto">
            <el-row :gutter="20" justify="space-between">
                <el-col :span="8">
                    <div>
                        <el-date-picker
                                v-model="date"
                                class="w-50 m-2"
                                type="date"
                                placeholder="Pick a day"
                                :disabled-date="disabledDate"
                                :shortcuts="shortcuts"
                                size="small"
                        />
                    </div>
                </el-col>
                <el-col :span="8">
                    <span class="ml-3 w-35 text-gray-600 inline-flex items-center">期望票数：</span>
                    <el-input-number v-model="quantity" size="small" :min="1" :max="10" controls-position="right"
                                     label="票数"/>

                </el-col>
                <el-col :span="8">
                    <span class="ml-3 w-35 text-gray-600 inline-flex items-center">间隔毫秒：</span>
                    <el-input-number v-model="interval" size="small" :min="1000" :max="100000" :step="500"
                                     controls-position="right"
                                     label="票数"/>
                </el-col>
            </el-row>

            <el-row class="row-bg" justify="space-between">
                <el-col :span="20">
                    <el-radio-group v-model="timePart">
                        <el-radio-button size="small" v-for="times in timeFragments" :label="times.label">{{
                            times.value
                            }}
                        </el-radio-button>
                    </el-radio-group>
                </el-col>

                <el-col :span="4">
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
            <el-card>
                <el-scrollbar ref="scrollbarRef" id="log-scroll">

                    <pre ref="innerRef">{{ logs.join('\n') }}</pre>


                </el-scrollbar>
            </el-card>
        </el-main>
        <!--        <el-footer>Footer</el-footer>-->
    </el-container>
</template>

<style scoped>

.flex {
    display: flex;
}

#log-scroll {
    height: 400px;
}

.title {
    font-weight: bold;
    color: #5BA3FA;
    font-size: 20px;
}

.head {
    background-color: #F8FAFC;
}

</style>
