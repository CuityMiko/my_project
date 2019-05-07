<template>
  <div
    v-loading="loading"
    element-loading-text="正在加载中..."
    element-loading-spinner="el-icon-loading">
    <el-form :inline="true" :model="formInline" style="margin-top: 30px; margin-bottom: -10px;">
      <el-form-item label="标 签">
        <el-select v-model="formInline.tagName" placeholder="请选择标签">
          <el-option
            v-for="item in tagData"
            :key="item.id"
            :label="item.typename"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="品 牌">
        <el-select v-model="formInline.brand" placeholder="请选择品牌">
          <el-option
            v-for="item in brandData"
            :key="item.id"
            :label="item.typename"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="分 组">
        <el-select v-model="formInline.groupName" placeholder="请选择分组">
          <el-option
            v-for="item in groupData"
            :key="item.id"
            :label="item.typename"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="search">查 询</el-button>
      </el-form-item>
    </el-form>
    <el-divider><i class="el-icon-search"></i></el-divider>
    <div class="content" v-if="dataSource.length > 0">
      <el-row>
        <el-col>
          <el-button-group class="right">
            <el-button size="mini" @click="sortBytime">添加时间<i :class="{'el-icon--right': true, 'el-icon-sort-up': sortBytimeUp, 'el-icon-sort-down': !sortBytimeUp}"></i></el-button>
            <el-button size="mini" @click="sortByprice">价格<i :class="{'el-icon--right': true, 'el-icon-sort-up': sortBypriceUp, 'el-icon-sort-down': !sortBypriceUp}"></i></el-button>
          </el-button-group>
        </el-col>
      </el-row>
      <el-row :gutter="10" v-for="(ritem, index) in dataSource" :key="index">
        <el-col :span="3">
            <el-popover
              placement="right-start"
              width="200"
              trigger="hover">
              <div>
                <p v-for="(rsales, index) in ritem.sales.substring(1, ritem.sales.length - 1).split('###')" :key="index" v-show="rsales != ''">{{rsales}}</p>
              </div>
              <el-card shadow="hover" style="margin-top: 10px;" slot="reference" :body-style="{ padding: '10px 0px 0px 0px' }">
                <div class="item">{{ritem.name || '-'}}</div>
                <br />
                <div :class="['item', 'price', {'hight': index <= 3, 'mid': index > 4, 'low': index > 8}]">
                  <img src="../assets/tickt.png" width="30" height="30" alt="" srcset="" v-if="index < 6">
                  <img src="../assets/low.png" width="30" height="30" alt="" srcset="" v-else>
                  <!-- <img src="../assets/down.png" width="30" height="30" alt="" srcset=""> -->
                  <span style="margin-left: 5px;">¥{{ritem.price || '-'}}</span>
                </div>
                <br />
                <div class="item discount">优惠券数量: {{ritem.salesCnt || '-'}}</div>
                <br />
                <div class="item flex">
                  <el-badge :value="ritem.hzSend" class="item" v-show="ritem.hzSend=='京'">
                  </el-badge>
                  <el-badge :value="ritem.hzSend" class="item" type="warning" v-show="ritem.hzSend!='京'">
                  </el-badge>
                  <span><i class="el-icon-time"></i><span style="margin-left: 3px;">{{ritem.addTime.split(' ')[1] || '-'}}</span></span>
                </div>
              </el-card>
            </el-popover>
        </el-col>
        <el-col v-for="(item, index) in ritem.appendList" :key="index" :span="3">
            <el-popover
              placement="right-start"
              width="200"
              trigger="hover">
              <div>
                <p v-for="(rsales, index) in item.sales.substring(1, item.sales.length - 1).split('###')" :key="index" v-show="rsales != ''">{{rsales}}</p>
              </div>
              <el-card shadow="hover" style="margin-top: 10px;" slot="reference" :body-style="{ padding: '10px 0px 0px 0px' }">
                <div class="item">{{item.name || '-'}}</div>
                <br />
                <div :class="['item', 'price', {'hight': index <= 3, 'mid': index > 4, 'low': index > 8}]">
                  <img src="../assets/tickt.png" width="30" height="30" alt="" srcset="" v-if="index < 6">
                  <img src="../assets/low.png" width="30" height="30" alt="" srcset="" v-else>
                  <!-- <img src="../assets/down.png" width="30" height="30" alt="" srcset=""> -->
                  <span style="margin-left: 5px;">¥{{item.price}}</span>
                </div>
                <br />
                <div class="item discount">优惠券数量: {{item.salesCnt}}</div>
                <br />
                <div class="item flex">
                  <el-badge :value="item.hzSend" class="item" v-show="item.hzSend=='京'">
                  </el-badge>
                  <el-badge :value="item.hzSend" class="item" type="warning" v-show="item.hzSend!='京'">
                  </el-badge>
                  <span><i class="el-icon-time"></i><span style="margin-left: 3px;">{{item.addTime.split(' ')[1] || '-'}}</span></span>
                </div>
              </el-card>
            </el-popover>
        </el-col>
      </el-row>
    </div>
    <div v-else>
      <el-divider content-position="center">暂无数据</el-divider>
    </div>
  </div>
</template>

<script>
import http from '../utils/http';
import axios from 'axios';

export default {
  name: 'Main',
  data () {
    return {
      loading: true,
      tagData: [], // 标签数据源
      groupData: [], // 分组数据源
      brandData: [], // 品牌数据源
      formInline: {
        tagName: '', // 标签
        groupName: '', // 分组
        brand: '' // 品牌
      },
      dataSource: [
      ],
      sortBytimeUp: true, // 按时间排序升序
      sortBypriceUp: false // 按价格排序升序
    }
  },
  mounted() { // 初始化
    var _self = this;
    _self.loadData();
    setInterval(() => {
      _self.loadData();
    }, 1000 * 60 * 5)
  },
  methods: {
    loadData() { // 加载数据
      var _self = this;
      axios.all([
        http({
          url: '/api/mrsm/tstype.do?getTypeDetail',
          method: 'post',
          params: {
            typegroupid: '2c5e5c466a687b54016a6cddef1d0065' // 标签
          }
        }),
        http({
          url: '/api/mrsm/tstype.do?getTypeDetail',
          method: 'post',
          params: {
            typegroupid: '2c5e5c466a687b54016a6cddadcc0063' // 品牌
          }
        }),
        http({
          url: '/api/mrsm/tstype.do?getTypeDetail',
          method: 'post',
          params: {
            typegroupid: '2c5e5c46696fa52e01696fb1a7990019' // 分组
          }
        }),
        _self.getList() // 获取数据源
      ]).then(axios.spread(function (tag, brand, group, list) {
        if (tag.data.ok) {
          _self.tagData = tag.data.data;
        }
        if (brand.data.ok) {
          _self.brandData = brand.data.data;
        }
        if (group.data.ok) {
          _self.groupData = group.data.data;
        }
        if (list.data.ok) {
          _self.dataSource = list.data.data;
        }
        _self.loading = false;
      }));
    },
    search() { // 查询
      var _self = this;
      _self.getList().then(list => {
        if (list.data.ok) {
          _self.dataSource = list.data.data;
        } else {
          _self.dataSource = [];
        }
      });
    },
    sortBytime() { // 按时间排序
      this.sortBytimeUp = !this.sortBytimeUp;
      if (dataSource.length > 0 && dataSource.appendList.length > 0) {
        let dataSource = JSON.parse(JSON.stringify(this.dataSource));
        if (this.sortBytimeUp) { // 升序
          this.dataSource = dataSource.appendList.sort((x, y) => x.addTime - y.addTime);
        } else { // 降序
            this.dataSource.appendList = dataSource.appendList.sort((x, y) => y.addTime - x.addTime);
        }
      }
    },
    sortByprice() { // 按价格排序
      this.sortBypriceUp = !this.sortBypriceUp;
      if (dataSource.length > 0 && dataSource.appendList.length > 0) {
        let dataSource = JSON.parse(JSON.stringify(this.dataSource));
        if (this.sortBypriceUp) { // 升序
          this.dataSource = dataSource.appendList.sort((x, y) => x.price - y.price);
        } else { // 降序
            this.dataSource.appendList = dataSource.appendList.sort((x, y) => y.price - x.price);
        }
      }
    },
    getList() { // 获取数据源
      var _self = this;
      return http({
        url: '/api/mrsm/jdptResultController.do?resultList',
        method: 'post',
        params: {
          groupName: _self.formInline.groupName, // 分组
          tagName: _self.formInline.tagName, // 分组
          brand: _self.formInline.brand // 分组
        }
      })
    }
  }
}
</script>

<style scoped>
  .right {
    float: right;
  }
  .content {
    background-color: rgb(182, 178, 178);
    padding: 10px;
  }
  .item {
    display: flex;
    margin-left: 15px;
    /* margin-bottom: -27px;
    margin-top: 5px;
    height: 45px; */
    font-size: 14px;
    line-height: 17px;
  }
  .flex {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-left: -1px;
    margin-right: 15px;
  }
  .discount {
    color: red;
    font-size: 13px;
  }
  .price {
    font-size: 24px;
    font-weight: 500;
    display: flex;
    flex-direction: row;
    align-items: center;
    margin-top: -6px;
    margin-bottom: -6px;
  }
  .hight {
    color: red;
  }
  .mid {
    color: green;
  }
  .low {
    color: orange;
  }
</style>
