<template>
<div>
    <el-collapse v-model="activeNames" @change="handleChange" >
        <el-collapse-item :name="key.key" v-for="key in detailKey" :key="key.key">
            <template slot="title">
                <img
                    :src="imgOne"
                    alt=""
                    class="icon ll"
                    style="margin-left: 10px; border: none; border-radius: 50%"
                >
                <span style="margin-left: 30px">{{key.key}}</span>
                <span style="margin-right: 50px; float: right">{{'星期' + turnWeek[key.week]}}</span>
            </template>
            
            <el-card shadow="hover" v-for="item in detailDic[key.key]" :key="item.txdate" class="box-card">
                <img :src="picDic[item.dir]" alt="类型图标" style="width: 50px;float: left;margin-right: 10px;">
                <el-tag type="danger" style="float: right;margin-top: 7px;color: #ff0000;">{{'¥' + Math.abs(item.txamt)}}</el-tag>
                <p>{{item.dir}}</p>
                <p style="font-size: 12px;color: #999;">{{item.shopname}}</p>
            </el-card>

        </el-collapse-item>
    </el-collapse>
</div>
</template>



<script>
let dayjs = require('dayjs');
import picOne from './../assets/u106.png'

import dirx from './../assets/dirx.png';
import dird from './../assets/dird.png';
import dirm from './../assets/dirm.png';
import dirs from './../assets/dirs.png';
import dirq from './../assets/dirq.png';

// 分类图标字典
const picDic = {
    "旭日苑" :dirx,
    "东升苑": dird,
    "美广": dirm,
    "超市": dirs,
    "其他": dirq,
    "无": dirq,
};

// 星期数字转化为汉字字典
const turnWeek = {
    "1": "一",
    "2": "二",
    "3": "三",
    "4": "四",
    "5": "五",
    "6": "六",
    "7": "日",
};

export default {
    data() {
        return {
            turnWeek: turnWeek,
            imgOne: picOne,
            picDic: picDic,
            activeNames: [],
            detailData: [],
            cost: '',
            detailDic: {},
            detailKey: 　[],
        }
    },
    mounted() {
        let _this = this
        _this.$http({
                url: "http://118.126.110.182:8002/api/getOneMonthData",
                method: "get",
                headers: {
                    "Content-Type": "application/x-www-form-urlencoded"
                },
            })
            .then(function (res) {
                if (res.data.status === 0) {
                    // 数据整理 按照日期对数据进行分类
                    let orderBydate = {};
                    let orderByKey = [];
                    for (let item of res.data.data) {
                        let theDay = dayjs(item.txdate).format('YYYY-MM-DD');
                        if (theDay in orderBydate) {
                            orderBydate[theDay].push(item);
                        } else {
                            orderByKey.push({
                                key: theDay,
                                week: dayjs(theDay).day().toString()
                            });
                            orderBydate[theDay] = [item];
                        }
                    }
                    _this.detailDic = orderBydate;
                    _this.detailKey = orderByKey;
                    // 默认打开第一个元素
                    _this.activeNames = [orderByKey[0].key];
                    _this.detailData = res.data.data;
                    _this.cost = res.data.cost;
                } else {
                    _this.$message.error("获取失败");
                }
            })
            .catch(function (error) {
                console.log(error);
            });
    }
}
</script>



<style scoped>
.bel-col {
    border-top: 1px solid #80808082;
}

.date {
    color: gray;
    font-size: 10px;
    padding-top: 5px;
}

.deatilCost {
    margin: 15px 0;
    font-size: 15px;
}

.top {
    margin: 2px 0;
}

.dirFont {
    position: absolute;
    bottom: 0;
    font-size: 12px;
    right: 31px;
}

.span {
    display: inline-block;
}

.dircolor {
    color: #e66b45;
}

.placeShop {
    font-size: 14px;
}

.shopName {
    font-size: 12px;
    color: gray;
}

.fontSize {
    font-size: 7px;
    color: gray;
}

.move {
    padding-left: 80px;
}

.moneyNum {
    margin-top: 40px;
}

.placeName {
    margin-top: 22px;
}

.dir {
    margin: 10px 0 5px;
}

.grid-content {
    background-color: #ebeef56e;
    min-height: 42px;
}
</style>
