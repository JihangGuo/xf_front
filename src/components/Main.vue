<template>
    <div>

        <el-row>
            <el-col :span="12">
                <div class="grid-content bg-purple cash">
                    <p class="text-cash">账户余额<br>
                        <span class="text-cash-span">{{dayData.balance}}</span>
                    </p>
                </div>
            </el-col>
            <el-col :span="12">
                <div class="grid-content bg-purple-light cash">
                    <p class="text-cash">{{checkTab}}月支出<br>
                        <span class="text-cash-span">{{parseInt(dayData.cost)}}</span>
                    </p>
                </div>
            </el-col>
        </el-row>

        <div class="con_flow">
            <img :src="imgRed" alt="" class="ll">
            <span class="ss">消费流水</span>
        </div>
        <el-row>
            <el-col :span="12">
                <div class="grid-content bg-purple">
                    <p class="date">{{dayData.daily}}</p>
                </div>
                <div class="conPlace">
                    <div class="place qq" v-for="item in dayData.data" :key="item.txdate">
                        <!-- <div class="imgdiv">
                        <img
                            src="./../assets/u13.jpg"
                            alt=""
                            class="img"
                        ></div> -->
                        <div class="pdiv">
                            <img src="" alt="图片" class="circle">
                            <p class="dir">{{item.dir}}</p>
                            <p class="shopname">{{item.shopname}}</p><br></div>
                    </div>
                </div>
            </el-col>
            <el-col :span="12">
                <div class="grid-content bg-purple-light right">
                    <p class="pay">支出{{dayData.pay}}</p>
                </div>
                <div class="content" v-for="item in dayData.data">
                    <p class="payDetail">{{item.txamt}}</p>
                </div>
            </el-col>
            <el-col :span="24">
                <div class="viewAll">
                    <p @click="goDetail" class="viewAll_a">查看全部 ></p>
                </div>
            </el-col>
        </el-row>

        <div class="pass">

        </div>

        <el-row type="flex" justify="center">
            <el-col :span="24">
                <div class="tabs">
                    <el-tabs v-model="checkTab" @tab-click="handleClick" class="tabsWidth">
                        <el-tab-pane :label="item.title" :name="item.title" style="width:100%;" class="eltab" v-for="item in monthLen" :key="item.title">月
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :span="9">
                <div id="pie"></div>
            </el-col>
            <el-col :span="15">
                <div class="">
                    <div class="vertical">
                        <div class="padding">
                            <img :src="imgOne" alt="" class="icon ll">
                            <p class="middle">旭日苑
                                <span class="middleInline">{{monthData.dirlist.dirx}}</span>
                            </p>
                        </div>
                        <div class="padding">
                            <img :src="imgTwo" alt="" class="icon ll">
                            <p class="middle">东升苑
                                <span class="middleInline">{{monthData.dirlist.dird}}</span>
                            </p>
                        </div>
                        <div class="padding">
                            <img :src="imgTwo" alt="" class="icon ll">
                            <p class="middle">美广
                                <span class="middleInline">{{monthData.dirlist.dirm}}</span>
                            </p>
                        </div>
                        <div class="padding">
                            <img :src="imgThree" alt="" class="icon ll">
                            <p class="middle">超市
                                <span class="middleInline">{{monthData.dirlist.dirs}}</span>
                            </p>
                        </div>
                        <div class="padding">
                            <img :src="imgFour" alt="" class="icon ll">
                            <p class="middle">其他
                                <span class="middleInline">{{monthData.dirlist.dirq}}</span>
                            </p>
                        </div>
                    </div>
                </div>
            </el-col>
        </el-row>

        <el-row type="flex" justify="center">
            <div id="demo" style="width: 100%; height: 500px; overflow: scorll"></div>
        </el-row>
    </div>
</template>





<script>
import * as echarts from 'echarts'
import Red from './../assets/u107.png'
import picOne from './../assets/u106.png'
import picTwo from './../assets/u109.png'
import picThree from './../assets/u143.png'
import picFour from './../assets/u108.png'

const dayjs = require('dayjs')

export default {
  data() {
    return {
      checkTab: 0,
      monthLen: 0,
      imgOne: picOne,
      imgTwo: picTwo,
      imgThree: picThree,
      imgFour: picFour,
      imgRed: Red,
      balance: '',
      cost: '',
      dayData: {
        pay: '',
        data: []
      },
      weekData: {},
      monthData: {
        dirlist: {
          dirx: '',
          dird: '',
          dirq: '',
          dirm: '',
          dirs: ''
        },
        toplist: {}
      },
      getPieOption: {
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b}: {c} ({d}%)'
        },
        color: ['#48cda6', '#968ade', '#fd87ab', '#FF69B4'], //,'#ffdf33'
        series: [
          {
            name: '数据来源',
            type: 'pie',
            radius: ['50%', '70%'],
            avoidLabelOverlap: false,
            label: {
              normal: {
                show: false,
                position: 'center'
              },
              emphasis: {
                show: false,
                textStyle: {
                  fontSize: '30',
                  fontWeight: 'bold'
                }
              }
            },
            labelLine: {
              normal: {
                show: false
              }
            },
            data: []
            // center: ['10%', '10%']
          }
        ]
      },
      getBarOption: {
        color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [
          {
            type: 'category',
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
            axisTick: {
              alignWithLabel: true
            }
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ],
        series: [
          {
            name: '消费数额',
            type: 'bar',
            barWidth: '60%',
            data: [10, 52, 200, 334, 390, 330, 220]
          }
        ]
      }
    }
  },
  created() {
    let monthLen = Number(dayjs().month()) + 1
    let resultMonth = []
    for (let i = 0; i < monthLen; i++) {
      resultMonth.push({
        title: i + 1
      })
    }
    this.monthLen = resultMonth
  },
  mounted() {
    this.drawLinePie() //扇形图
    // 初始化月份
    this.checkTab = dayjs().month() +1 
    let _this = this
    // let enddate = dayjs().format("YYYY-MM-DD");
    // let begindate= dayjs().subtract(1,'month').format("YYYY-MM-DD");

    // 获取余额 截止到上个月当前日期
    _this
      .$http({
        url: 'http://118.126.110.182:8002/api/getNewData',
        method: 'get',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
        // params: {
        //     begindate,
        //     enddate,
        // }
      })
      .then(function(res) {
        if (res.data.status === 0) {
          _this.dayData.balance = res.data.data[0].balance
          // _this.dayData.cost = res.data.cost
          _this.dayData.pay = parseInt(res.cost)
          // _this.dayData.dirOne = res.data.data[0].dir
          // _this.dayData.daily = res.data.data[0].txdate
          // _this.dayData.shopnameOne = res.data.data[0].shopname
          // _this.dayData.txamtOne = res.data.data[0].txamt
        } else {
          _this.$message.error('获取失败')
        }
      })
      .catch(function(error) {
        console.log(error)
      })

    // 获取本月支出数据
    _this
      .$http({
        url: 'http://118.126.110.182:8002/api/getOneMonthData',
        method: 'get',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      })
      .then(function(res) {
        if (res.data.status === 0) {
          _this.dayData.cost = parseInt(res.data.cost)
        } else {
          _this.$message.error('获取失败')
        }
      })
      .catch(function(error) {
        console.log(error)
      })

    // 消费列表详情
    _this
      .$http({
        url: 'http://118.126.110.182:8002/api/getNewData',
        method: 'get',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      })
      .then(function(res) {
        if (res.data.status === 0) {
          _this.dayData.pay = parseInt(res.data.cost)
          _this.dayData.data = res.data.data.slice(0, 3)
        } else {
          _this.$message.error('获取失败')
        }
      })
      .catch(function(error) {
        console.log(error)
      })
  },
  methods: {
    // 路由跳转详情页
    goDetail() {
      this.$router.push('/detail')
    },
    handleClick(tab) {
      this.checkTab = tab.name

      // 根据tab值生成对应的年月日
      let getYear = dayjs().year() + '-' + this.checkTab + '-' + '01'

      let begindate = dayjs(getYear)
        .startOf('month')
        .format('YYYY-MM-DD')
      let enddate = dayjs(getYear)
        .endOf('month')
        .format('YYYY-MM-DD')

      this.drawLinePie({
        begindate,
        enddate
      })
    },
    //异步axios请求数据，完成扇形统计图
    drawLinePie(monthData) {
      this.linePie = echarts.init(document.getElementById('pie'))
      this.linePie.setOption(this.getPieOption)
      let _this = this
      // 获取当月月初和月底时间
      let begindate = dayjs()
        .startOf('month')
        .format('YYYY-MM-DD')
      let enddate = dayjs()
        .endOf('month')
        .format('YYYY-MM-DD')
      if (monthData) {
        begindate = monthData.begindate
        enddate = monthData.enddate
      }

      _this
        .$http({
          url: 'http://118.126.110.182:8002/api/getNewData',
          method: 'get',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          params: {
            begindate,
            enddate
          }
        })
        .then(function(res) {
          if (res.data.status === 0) {
            // 按区域消费统计
            _this.monthData.dirlist = res.data.dirlist[0]
            // 按吃货分类统计
            _this.monthData.toplist = res.data.toplist
            _this.linePie.setOption({
              title: {
                //标题组件
                text: '支出分类',
                top: '10px',
                left: '20px', //标题的位置 默认是left，其余还有center、right属性
                textStyle: {
                  color: '#696969',
                  fontSize: 14
                }
              },
              graphic: {
                type: 'text',
                left: 'center',
                top: 'center',
                style: {
                  text: parseInt(res.data.cost) + '\r\n总支出',
                  textAlign: 'center',
                  fill: '#000',
                  width: 30,
                  height: 30
                },
                textStyle: {
                  fontSize: 15,
                  color: '#39CCCC'
                }
              },
              tooltip: {
                position: ['50%', '50%']
              },
              series: [
                {
                  radius: ['45%', '60%'],
                  data: [
                    {
                      value: res.data.dirlist[0].dirx,
                      name: '旭日苑'
                    },
                    {
                      value: res.data.dirlist[0].dird,
                      name: '东升苑'
                    },
                    {
                      value: res.data.dirlist[0].dirm,
                      name: '美广'
                    },
                    {
                      value: res.data.dirlist[0].dirs,
                      name: '超市'
                    },
                    {
                      value: res.data.dirlist[0].dirq,
                      name: '其他'
                    }
                  ]
                }
              ]
            })
            _this.drawLineBar() //柱状图
          } else {
            _this.$message.error('获取失败')
          }
        })
        .catch(function(error) {
          console.log(error)
        })
    },
    // 加载柱状图
    drawLineBar() {
      this.lineBar = echarts.init(document.getElementById('demo'))
      this.lineBar.setOption(this.getBarOption)
      // 对吃货种类进行处理 两类数组一一对应
      let titleArr = []
      let valueArr = []
      console.log(this.monthData.toplist)
      for (let item of this.monthData.toplist) {
        titleArr.push(item.shopname)
        valueArr.push(item.sum)
      }
      console.log(titleArr, valueArr)
      this.lineBar.setOption({
        xAxis: {
          data: titleArr
        },
        series: {
          data: valueArr
        }
      })
    }
  }
}
</script>






<style scoped>
.payDetail {
  padding: 5px 0;
}

.padding {
  padding: 5px 15px;
}

.vertical {
  margin-top: 30px;
}

.middleInline {
  display: inline;
  /* vertical-align: middle; 居中对齐， */
  position: absolute;
  right: 30px;
}

.icon {
  vertical-align: middle;
  /* 居中对齐， */
  margin-left: 40px;
}

.middle {
  vertical-align: middle;
  /* 居中对齐， */
  display: inline-block;
  margin-left: 10px;
  width: 60%;
  font-size: 14px;
}

.tabsWidth {
  font-size: 13px;
  height: 33px;
}

#pie {
  width: 200px;
  height: 200px;
}

.qq {
  overflow: auto;
}

.img {
  width: 40px;
  height: 40px;
}

.imgdiv {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  float: left;
  margin-right: 15px;
  overflow: hidden;
  margin-top: 4px;
}

.pdiv {
  float: left;
  padding-top: 8px;
  height: 50px;
  box-sizing: border-box;
}

.ll {
  display: inline-block;
  width: 5px;
  height: 5px;
  border-radius: 50%;
}

.viewAll {
  text-align: center;
}

.viewAll_a {
  text-decoration: none;
  color: #908d8d;
  cursor: pointer;
}

.tabs {
  display: flex;
  justify-content: center;
  width: 100%;
  /* width: 100% */
}

.pass {
  background-color: #dcdcdc;
  height: 15px;
  margin-top: 5px;
}

.pay {
  padding: 5px 45px;
  text-align: end;
  font-size: 15px;
  background-color: #dcdcdc;
}

.payDetail {
  padding: 0px;
  font-size: 14px;
  height: 50px;
  line-height: 50px;
  text-align: center;
  padding-left: 80px;
}

.date {
  padding: 5px 20px;
  font-size: 14px;
  background-color: #dcdcdc;
}

.conPlace {
  margin-left: 20px;
}

.shopname {
  color: gray;
  font-size: 12px;
}

.dir {
  font-size: 14px;
}

.con_flow {
  margin: 10px 0 10px 25px;
}

.text-cash-span {
  font-size: 22px;
}

.text-cash {
  text-align: center;
  vertical-align: middle;
}

#demo {
  display: flex;
  justify-content: center;
}

.bg-purple {
  background: #e0ffff;
}

.bg-purple-light {
  background: #e0ffff;
}

.grid-content {
  border-radius: 4px;
  min-height: 25px;
}
</style>
