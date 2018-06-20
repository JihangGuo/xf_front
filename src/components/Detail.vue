<template>
<div>
    <el-collapse v-model="activeNames" @change="handleChange" >
        <el-collapse-item :name="key.key" v-for="key in detailKey" :key="key.key">
            <template slot="title">
                <span>{{key.key}}</span>
                <span>{{'星期' + key.week}}</span>
            </template>
            
            <el-card shadow="hover" v-for="item in detailDic[key.key]" :key="item.txdate" class="box-card">
                <img :src="picDic[item.dir]" alt="类型图标">
                <el-tag style="float: right">{{'¥' + Math.abs(item.txamt)}}</el-tag>
                <span>{{item.dir}}</span>
                <span>{{item.shopname}}</span>
            </el-card>

        </el-collapse-item>
    </el-collapse>
</div>
</template>



<script>
let dayjs = require('dayjs');

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

export default {
    data() {
        return {
            picDic: picDic,
            activeNames: ['1'],
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
                                week: dayjs(theDay).day()
                            });
                            orderBydate[theDay] = [item];
                        }
                    }
                    _this.detailDic = orderBydate;
                    _this.detailKey = orderByKey;
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
