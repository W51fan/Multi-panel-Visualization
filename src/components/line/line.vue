<!-- 层叠柱状图 -->
<style lang="stylus" scoped>
.line {
  height: 1000px;
  background: url('../../assets/bg.jpg') no-repeat;
  background-size: 100% 100%;

  .main {
    width: 100%;
    height: calc(100% - 100px);
    margin-top: -15px;
  }
}
</style>

<template>
  <div class="line">
    <v-header :name="name" :legendArr="legendArr" :myChart="myChart"></v-header>
    <v-filter :myChart="myChart" v-if="myChart._dom"></v-filter>
    <div class="main"></div>
  </div>
</template>

<script>
import echarts from "echarts";
import header from "@/components/header/header";
import filter from "@/components/filter/filter";

export default {
  data() {
    return {
      legendArr: [],
      color: this.$store.state.color,
      myChart: {},
      name: "实际产能"
    };
  },
  methods: {
    initFun() {
      this.legendArr = this.myChart.getOption().series;
      this.legendArr.forEach(data => {
        data.selected = true;
      });
      this.$root.charts.push(this.myChart);
      window.addEventListener(
        "resize",
        function() {
          this.myChart.resize();
        }.bind(this)
      );
    }
  },
  components: {
    "v-header": header,
    "v-filter": filter
  },
  mounted() {
    // 基于准备好的dom，初始化echarts实例
    var category = ["2018/01", "2018/02", "2018/03", "2018/04", "2018/05"]; //当前前12个月每月月份
    var lineData = [700, 750, 850, 1020, 850]; //总产能
    var barData1 = [200, 300, 400, 500, 600]; //正常产能
    var barData2 = [300, 200, 100, 400, 200]; //加班产能
    var barData3 = [200, 250, 350, 120, 50];
    this.myChart = echarts.init(document.querySelector(".line .main"));
    this.myChart.setOption({
      // backgroundColor: '#fff',
        tooltip: {
            trigger: 'axis',
            axisPointer: {
                type: 'shadow',
                label: {
                    show: true,
                    backgroundColor: '#333'
                }
            },
            formatter: '{a0}: {c0}<br />{a1}: {c1}<br />{a2}: {c2}<br />{a3}: {c3}'
        },
        legend: {
            data: ['红灯时间', '故障', '缺料', '停机'],

        },
        xAxis: {
            data: category,
            axisLine: {
                lineStyle: {
                    color: '#ccc'
                }
            }
        },
        yAxis: {
            splitLine: {
                show: false
            },
            axisLine: {
                lineStyle: {
                    color: '#ccc'
                }
            }
        },
        series: [{
                name: '红灯时间',
                type: 'line',
                smooth: true,
                showAllSymbol: true,
                symbol: 'emptyCircle',
                symbolSize: 10,
                label: {
                    normal: {
                        show: true,
                        position: 'top'
                    }
                },
                data: lineData
            },
            {
                name: '故障',
                type: 'bar',
                barWidth: 20,
                stack: '类型',
                itemStyle: {
                    normal: {
                        barBorderRadius: 5,
                        color: new echarts.graphic.LinearGradient(0,
                            0, 0, 1, [{
                                offset: 0,
                                color: 'rgba(255,99,71,0.8)'
                            }, {
                                offset: 1,
                                color: 'red'
                            }])
                    }
                },
                data: barData1
            },
            {
                name: '缺料',
                type: 'bar',
                barWidth: 20,
                stack: '类型',
                itemStyle: {
                    normal: {
                        color: new echarts.graphic.LinearGradient(0,
                            0, 0, 1, [{
                                    offset: 0,
                                    color: 'rgba(238,238,0,0.8)'
                                },
                                // {
                                //     offset: 0.5,
                                //     color: '#FFCC00'
                                // },
                                {
                                    offset: 1,
                                    color: 'rgba(238,238,0,0.3)'
                                }
                            ])
                    }
                },
                data: barData2
            },
            {
                name: '停机',
                type: 'bar',
                barWidth: 20,
                stack: '类型',
                itemStyle: {
                    normal: {
                        barBorderRadius: 5,
                        color: new echarts.graphic.LinearGradient(0,
                            0, 0, 1, [{
                                offset: 0,
                                color: 'rgba(190,190,190,0.8)'
                            }, {
                                offset: 1,
                                color: 'rgba(190,190,190,0.3)'
                            }])
                    }
                },
                data: barData3
            },
            {
                name: 'dotted',
                type: 'pictorialBar',
                symbol: 'rect',
                itemStyle: {
                    normal: {
                        color: '#EE2C2C'
                    }
                },
                symbolRepeat: true,
                symbolSize: [12, 4],
                symbolMargin: 1,
                z: -10,
                data: lineData
            }
        ]
    });
    this.initFun();
  }
};
</script>
