<script setup>
// 导入模型解析构造函数
import { Application } from '@splinetool/runtime'

import { onMounted, ref  } from 'vue'
// 导入loading组件 
import LoadingCompone from '@/views/components/LoadingComponent.vue'
// import { getParkInfoAPI } from '@/apis/park'
// import * as echarts from 'echarts'

// import {useInitParkInfo, useInitBartChart, useInitPieChart, useInit3d } from './composables/userjs'
import {useInitParkInfo, useInitBartChart, useInitPieChart } from './composables/userjs'
// 获取园区数据
const { parkInfo, getParkInfo } = useInitParkInfo()

// 渲染年度收入分析2d图表
const { initBarChart, barChart } = useInitBartChart(parkInfo)

// 渲染园区产业分布2d图标
const { initPieChart, pieChart } = useInitPieChart(parkInfo)


// 调用接口拉取3d模型 渲染视图中
const publisPath = 'https://fe-hmzs.itheima.net'
const ref3d = ref(null)
const showLoading = ref(false)
const init3dModel = () => {
  // 开启loading 
  showLoading.value = true
  // 1. 实例解析器对象(传入模型将来渲染的节点对象)
  const spline = new Application(ref3d.value)
  // 2. 使用对象load方法去拉取3大模型资源
  spline.load(`${publisPath}/scene.splinecode`).then(() => {
    showLoading.value = false
    // 模型渲染完毕之后后续的逻辑操作
    // 3. 拉取资源之后.then方法中可以做后续的逻辑操作
  })
}


// 调度图表渲染
onMounted(async () => {
  // 保证数据先回来
  await getParkInfo()
  initBarChart()
  initPieChart()
  init3dModel()
})

// const parkInfo = ref({})
// const getParkInfo = async () => {
//     const res = await getParkInfoAPI()
//     parkInfo.value = res.data
// }
// // getParkInfo()

// const barChart = ref(null)
// // 渲染年度收入分析图表
// const initBarChart = () => {
//   // 1. 解构图表数据
//   const { parkIncome } = parkInfo.value
// //   parkIncome.yIncome = [100,80,90,70,100,80,90,70,100,80,90,70]
//   // 2. 准备options数据
//   const barOptions = {
//     tooltip: {
//       trigger: 'axis',
//       axisPointer: {
//         type: 'shadow',
//       },
//     },
//     grid: {
//       // 让图表占满容器
//       top: '10px',
//       left: '0px',
//       right: '0px',
//       bottom: '0px',
//       containLabel: true,
//     },
//     xAxis: [
//       {
//         type: 'category',
//         axisTick: {
//           alignWithLabel: true,
//           show: false,
//         },
//         data: parkIncome.xMonth,
//       },
//     ],
//     yAxis: [
//       {
//         type: 'value',
//         splitLine: {
//           show: false,
//         },
//       },
//     ],
//     series: [
//       {
//         name: '园区年度收入',
//         type: 'bar',
//         barWidth: '10px',
//         data: parkIncome.yIncome.map((item, index) => {
//           const color =
//             index % 2 === 0
//               ? new echarts.graphic.LinearGradient(0, 0, 0, 1, [
//                 { offset: 0.23, color: '#74c0f8' },
//                 { offset: 1, color: 'rgba(116,192,248,0.00)' },
//               ])
//               : new echarts.graphic.LinearGradient(0, 0, 0, 1, [
//                 { offset: 0.23, color: '#ff7152' },
//                 { offset: 1, color: 'rgba(255,113,82,0.00)' },
//               ])
//           return { value: item, itemStyle: { color } }
//         }),
//       },
//     ],
//     textStyle: {
//       color: '#B4C0CC',
//     },
//   }
//   // 3. 渲染图表
//   const myBarChart = echarts.init(barChart.value)
//   myBarChart.setOption(barOptions)
// }
// onMounted(async()=>{
//     await getParkInfo()
//     initBarChart()
//     initPieChart()
// })


// // 渲染园区产业分布图表
// const pieChart = ref(null)
// const initPieChart = () => {
//   const { parkIndustry } = parkInfo.value
//   const pieOption = {
//     color: [
//       '#00B2FF', '#2CF2FF', '#892CFF', '#FF624D', '#FFCF54', '#86ECA2'],
//     legend: {
//       itemGap: 20,
//       bottom: '0',
//       icon: 'rect',
//       itemHeight: 10, // 图例icon高度
//       itemWidth: 10, // 图例icon宽度
//       textStyle: {
//         color: '#c6d1db',
//       },
//     },
//     tooltip: {
//       trigger: 'item'
//     },
//     series: [
//       {
//         name: '园区产业分析',
//         type: 'pie',
//         radius: ['55%', '60%'], // 设置内圈与外圈的半径使其呈现为环形
//         center: ['50%', '40%'], // 圆心位置， 用于调整整个图的位置
//         tooltip: {
//           trigger: 'item',
//           formatter: (params) => {
//             return `${params.seriesName}</br><div style='display:flex;justify-content: space-between;'><div>${params.marker}${params.name}</div><div>${params.percent}%</div></div>`
//           }
//         },
//         label: {
//           show: false,
//           position: 'center',
//         },
//         data: parkIndustry,
//       },
//     ],

//   }
//   const myPieChart = echarts.init(pieChart.value)
//   myPieChart.setOption(pieOption)
// }
</script>

<template>
    <div class="all-charts">
        <!-- 园区概况 -->
        <div class="section-one">
            <img class="img-header"
                src="https://yjy-teach-oss.oss-cn-beijing.aliyuncs.com/smartPark/%E5%A4%A7%E5%B1%8F%E5%88%87%E5%9B%BE/%E5%9B%AD%E5%8C%BA%E6%A6%82%E5%86%B5%402x.png"
                alt="" />
            <div class="icons-container">
                <div class="item">
                    <div class="icons-item building-icon">
                        <span class="number">
                            <!-- 
                首次渲染（后端数据没有返回）
                parkInfo - {} - {}.base -> undefined -> undefined.buidingTotal
                
                二次渲染（后端数据返回之后 响应式状态发生变化 再次引起模版渲染）

                场景：访问一个空对象多层次的属性
                解决办法：可选链?.   保证必须前面有值才会继续往后进行点运算
               -->
                            {{ parkInfo.base?.buildingTotal }}
                        </span>
                    </div>
                    <span class="title">楼宇总数</span>
                    <span class="unity">（栋）</span>
                </div>
                <div class="item">
                    <div class="icons-item enterprise-icon">
                        <span class="number">
                            {{ parkInfo.base?.enterpriseTotal }}
                        </span>
                    </div>
                    <span class="title">入驻企业总数</span>
                    <span class="unity">（家）</span>
                </div>
                <div class="item">
                    <div class="icons-item car-icon">
                        <span class="number">
                            {{ parkInfo.base?.parkingTotal }}
                        </span>
                    </div>
                    <span class="title">车位总数</span>
                    <span class="unity">（个）</span>
                </div>
                <div class="item">
                    <div class="icons-item rod-icon">
                        <span class="number">
                            {{ parkInfo.base?.chargePoleTotal }}
                        </span>
                    </div>
                    <span class="title">一体杆总数</span>
                    <span class="unity">（个）</span>
                </div>
                <!-- 园区年度收入分析 -->
                <div class="section-two">
                    <img class="img-header"
                        src="https://yjy-teach-oss.oss-cn-beijing.aliyuncs.com/smartPark/%E5%A4%A7%E5%B1%8F%E5%88%87%E5%9B%BE/%E5%9B%AD%E5%8C%BA%E5%B9%B4%E5%BA%A6%E6%94%B6%E5%85%A5%E5%88%86%E6%9E%90%402x.png"
                        alt="" />
                    <div class="bar-chart-titile">
                        <span>单位：元</span>
                        <div>
                            <span class="bar-icon blue-bar-icon"></span>
                            <span class="bar-icon red-bar-icon"></span>
                            收入情况
                        </div>
                    </div>
                    <div class="bar-chart" ref="barChart"></div>
                </div>
                <!-- 园区产业分布 -->
                <div class="section-three">
                <img class="img-header"
                    src="https://yjy-teach-oss.oss-cn-beijing.aliyuncs.com/smartPark/%E5%A4%A7%E5%B1%8F%E5%88%87%E5%9B%BE/%E5%9B%AD%E5%8C%BA%E4%BA%A7%E4%B8%9A%E5%88%86%E5%B8%83%402x.png"
                    alt="" />
                <div class="pie-chart" ref="pieChart"></div>
                </div>
            </div>
        </div>
    </div>
    <!-- <div v-if="loading" class="loading">
        <p class="text">园区资源加载中…</p>
        <div class="loading-process">
            <div class="process-wrapper"></div>
        </div>
    </div> -->
     <!-- 3d模型渲染节点 -->
    <div class="model-container">
        <!-- 进度条 -->
        <LoadingComponent :loading="showLoading" />
        <!-- 准备3D渲染节点 -->
        <canvas class="canvas-3d" ref="ref3d" />
    </div>
    
</template>

<style>
.all-charts {
    position: absolute;
    top: 0;
    left: 0;
    width: 480px;
    height: 100vh;
    padding: 20px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    background: linear-gradient(to left, rgba(0, 6, 15, 0.00) 0%, rgba(0, 6, 15, 0.00) 20%, rgba(0, 0, 0, 0.40) 30%, rgba(0, 0, 0, 0.60) 40%, rgba(1, 4, 11, 1) 70%, #04070d 100%);

    .img-header {
        height: 30px;
    }
}



.section-one {
    flex-basis: 25%;

    .icons-container {
        display: flex;
        justify-content: space-between;
        padding: 20px 0;

        .item {
            text-align: center;
            display: flex;
            flex-direction: column;
            flex: 1;

            .icons-item {
                height: 80px;
                position: relative;

                .number {
                    position: absolute;
                    left: 50%;
                    transform: translateX(-50%);
                    font-size: 18px;
                    font-family: FontquanXinYiGuanHeiTi, FontquanXinYiGuanHeiTi-Regular;
                    color: #ffffff;
                }
            }

            .building-icon {
                background: url('@/assets/building-icon.png') no-repeat 50% 0 / contain;
            }

            .enterprise-icon {
                background: url('@/assets/enterprise-icon.png') no-repeat 50% 0 / contain;
            }

            .rod-icon {
                background: url('@/assets/rod-icon.png') no-repeat 50% 0 / contain;
            }

            .car-icon {
                background: url('@/assets/car-icon.png') no-repeat 50% 0 / contain;
            }

            .title,
            .unity {
                font-size: 14px;
                color: #cdd7e1;
            }

            .title {
                margin-top: 8px;
            }
        }
    }
}

.section-two {
    flex-basis: 35%;
    .bar-chart {
      width: 100%;
      height: calc(100% - 70px);
    }
  }
.section-three {
flex-basis: 40%;

.pie-chart {
    position: relative;
    margin: 0 auto;
    padding-bottom: 20px;
    width: 80%;
    height: calc(100% - 40px);
}
}

.model-container {
  height: 100%;
  background-color: black;
  width: 100%;
  flex-shrink: 0;
}

</style>