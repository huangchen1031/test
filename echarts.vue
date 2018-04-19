<template>
  <chart :options="options" :init-options="initOptions" :auto-resize="true"></chart>
</template>

<script>
export default {
  name: 'echarts',
  props: {
    title: {
      type: String,
      default: '单据量统计',
    },
  },
  data() {
    return {
      initOptions: {
        renderer: 'svg',
      },
      options: {
        title: {
          text: '单据量统计',
          left: 100,
          top: 10,
        },
        legend: {
          bottom: 10,
          left: 100,
          data: ['待复审', '已审', '驳回'],
        },
        tooltip: {},
        xAxis: {
          data: ['部门费用', '员工借款单', '员工费用', '国内差旅', '国外差旅', '员工还款单'],
        },
        yAxis: {},
        series: [
          {
            name: '待复审',
            type: 'bar',
            data: [299, 422, 392, 300, 429, 393],
            itemStyle: {
              color: '#4bcdfe',
            },
          },
          {
            name: '已审',
            type: 'bar',
            data: [356, 250, 285, 370, 253, 271],
            itemStyle: {
              color: '#ffe760',
            },
          },
          {
            name: '驳回',
            type: 'bar',
            data: [200, 178, 190, 189, 168, 179],
            itemStyle: {
              color: '#ff8687',
            },
            barCategoryGap: '80%',
          },
        ],
      },
    };
  },
};
</script>

<style lang="scss">
.echarts {
  width: 100%;
  height: 350px;
  background-color: #fff;
}
</style>
