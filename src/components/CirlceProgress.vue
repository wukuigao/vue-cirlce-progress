<script setup lang="ts">
  import { ref, onMounted, computed, watch, reactive } from 'vue'
  import baoJingIcon from '../assets/baojing@3x.png'
  import weiZhiIcon from '../assets/weizhi@3x.png'
  import zhengChangIcon from '../assets/zhengchang@3x.png'

  function debounce(func, wait, immediate) {
    let timeout;
    return function () {
      const context = this;
      const args = [...arguments];
      if (timeout) clearTimeout(timeout);
      if (immediate) {
        const callNow = !timeout;
        timeout = setTimeout(() => {
          timeout = null;
        }, wait)
        if (callNow) func.apply(context, args)
      }
      else {
        timeout = setTimeout(() => {
          func.apply(context, args)
        }, wait);
      }
    }
  }

  const statusObjConfig = {
    noData: {
      statusText: '未知',
      icon: weiZhiIcon,
      color: '#8989A6',
      text: '异常分数',
    },
    normal: {
      statusText: '正常',
      icon: zhengChangIcon,
      color: '#7DC2F5',
      text: '正常分数',
    },
    warn: {
      statusText: '报警',
      icon: baoJingIcon,
      color: '#ff0000',
      text: '异常分数',
    }
  }

  defineProps<{ msg: string }>()

  const percent = ref(75)
  let chart = '';

  const dataObj = computed(() => {
  console.log('aaa')
  if(percent.value == 0){
    return statusObjConfig.noData
  }else if(percent.value > 0 && percent.value < 50){
    return statusObjConfig.normal
  }else{
    return statusObjConfig.warn
  }

})

  onMounted(()=> {
  chartInit()

})

  const chartInit = () => {
  chart = new G2.Chart({
    container: 'container',
    theme: 'classic',
    width: 400,
    height: 400,
  });

  chart
      .data([
        {
          name: 'activity1',
          percent: 0.7,
          color: '#1ad5de',
        },

      ])
      .coordinate({ type: 'radial', innerRadius: 0.2 });

  chart
      .interval()
      .encode('x', 'name')
      .encode('y', 1)
      .encode('size', 64)
      .encode('color', '#e6e8f0')
      .style('shadowColor', '#ccc')
      .style('shadowBlur', 10)
      .style('shadowOffsetY', 10)
      .style('shadowOffsetX', 0)
      .animate(false)
      .tooltip(false)

  chart
      .interval()
      .encode('x', 'name')
      .encode('y', 'percent')
      .encode('color', 'l(90) 0:#8a66ef  1:#bca9f4')
      .encode('size', 80)
      .style('radius', 15)
      .axis(false)
      .animate('enter', {
        type: 'waveIn',
        easing: 'easing-out-bounce',
        duration: 500,
      })
      .tooltip(false)


  chart.render();
}
  const renderChartHandle = debounce((val)=> {
    chart.changeData([{
      name: 'activity1',
      percent: val/100,
    }]);
  }, 500)

  watch([percent,], (val) => {

    renderChartHandle(val)
  },{})

  const marks = reactive<Marks>({
    25: '25%',
    50: {
      style: {
        color: '#ff0000',
      },
      label: '50%',
    },
    75: {
      style: {
        color: '#ff0000',
      },
      label: '75%',
    },
  })


</script>

<template>
  <div class="main">
    <div class="chart_num">
      <div>{{percent}}%</div>
      <div class="chart_num_text">{{dataObj.text}}</div>
    </div>
    <div id="container"></div>
  </div>
  <div class="status_box">
    <img class="status_box_img" :src="dataObj.icon" />
    <div class="status_box_txtbox">
      <div>检测状态</div>
      <div class="status_box_status" :style="{color: dataObj.color}" >{{dataObj.statusText}}</div>
    </div>

  </div>

  <el-slider v-model="percent" :marks="marks" />

</template>

<style scoped>
  .main{
    position: relative;
  }
  .chart_num{
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #7566f6;
    font-size: 60px;
  }
  .chart_num_text{
    font-size: 20px;
    color: #49487B;
  }
  .status_box{
    color: #666;
    display: flex;
    height: 92px;
    justify-content: center;
    align-items: center;
    margin-bottom: 20px;
  }
  .status_box_img{
    width: 92px;
    height: 92px;
  }
  .status_box_txtbox{
    font-size: 22px;
    line-height: 35px;
    text-align: left;
    margin-left: 18px;

  }
  .status_box_status{
    font-weight: bold;
  }
</style>
