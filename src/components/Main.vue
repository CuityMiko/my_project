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
    <el-divider><i class="el-icon-tickets"></i></el-divider>
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
        <el-col :span="3" @click.native="goLink(ritem.url)">
            <el-popover
              placement="right-start"
              width="200"
              trigger="hover">
              <div>
                <p v-for="(rsales, index) in ritem.sales.split(',')" :key="index" v-show="rsales != ''">{{rsales}}</p>
              </div>
              <el-card shadow="hover" style="margin-top: 10px; cursor: pointer;" slot="reference" :body-style="{ padding: '10px 0px 0px 0px' }">
                <div class="item">{{ritem.name.substr(0, 25).concat('...') || '-'}}</div>
                <br />
                <div :class="['item', 'price', {'hight': ritem.color == 'red', 'mid': ritem.color == 'green', 'low': ritem.color == 'yellow', 'down': ritem.color == 'gray'}]">
                  <img src="../assets/up.png" width="30" height="30" alt="" srcset="" v-show="ritem.color == 'red'">
                  <img src="../assets/low.png" width="30" height="30" alt="" srcset="" v-show="ritem.color == 'yellow'">
                  <img src="../assets/down.png" width="30" height="30" alt="" srcset="" v-show="ritem.color == 'green'">
                  <img src="../assets/shixiao.png" width="30" height="30" alt="" srcset="" v-show="ritem.color == 'gray'">
                  <span style="margin-left: 5px;">¥{{ritem.price || '-'}}</span>
                </div>
                <br />
                <div class="item discount">优惠券数量: {{ritem.salesCnt || '-'}}</div>
                <el-divider></el-divider>
                <el-row type="flex" justify="space-between">
                  <el-col :span="8">
                    <div class="jing" v-if="ritem.hzSend=='京'">{{ritem.hzSend}}</div>
                    <div class="shang" v-else>{{ritem.hzSend}}</div>
                  </el-col>
                  <el-col :span="8">
                    <el-tooltip class="item" effect="dark" content="点击查看详情" placement="bottom">
                      <i class="el-icon-view" style="cursor: pointer;" @click.stop="seeDetail(ritem)"></i>
                    </el-tooltip>
                  </el-col>
                  <el-col :span="8">
                    <span>
                      <i class="el-icon-time"></i>
                      <span style="font-size: 10px;">{{ritem.addTime.split(' ')[1].split(':')[0] + ':' + ritem.addTime.split(' ')[1].split(':')[1] || '-'}}</span>
                    </span>
                  </el-col>
                </el-row>
              </el-card>
            </el-popover>
        </el-col>
        <el-col v-for="(item, index) in ritem.appendList" :key="index" :span="3" @click.native="goLink(item.url)">
            <el-popover
              placement="right-start"
              width="200"
              trigger="hover">
              <div>
                <p v-for="(rsales, index) in item.sales.split(',')" :key="index" v-show="rsales != ''">{{rsales}}</p>
              </div>
              <el-card shadow="hover" style="margin-top: 10px; cursor: pointer;" slot="reference" :body-style="{ padding: '10px 0px 0px 0px' }">
                <div class="item">{{item.name.substr(0, 25).concat('...') || '-'}}</div>
                <br />
                <div :class="['item', 'price', {'hight': item.color == 'red', 'mid': item.color == 'green', 'low': item.color == 'yellow', 'down': item.color == 'gray'}]">
                  <img src="../assets/up.png" width="30" height="30" alt="" srcset="" v-show="item.color == 'red'">
                  <img src="../assets/low.png" width="30" height="30" alt="" srcset="" v-show="item.color == 'yellow'">
                  <img src="../assets/down.png" width="30" height="30" alt="" srcset="" v-show="item.color == 'green'">
                  <img src="../assets/shixiao.png" width="30" height="30" alt="" srcset="" v-show="item.color == 'gray'">
                  <span style="margin-left: 5px;">¥{{item.price || '-'}}</span>
                </div>
                <br />
                <div class="item discount">优惠券数量: {{item.salesCnt}}</div>
                <el-divider></el-divider>
                <el-row type="flex" justify="space-between">
                  <el-col :span="8">
                    <div class="jing" v-if="item.hzSend=='京'">{{item.hzSend}}</div>
                    <div class="shang" v-else>{{item.hzSend}}</div>
                  </el-col>
                  <el-col :span="8">
                    <el-tooltip class="item" effect="dark" content="点击查看详情" placement="bottom">
                      <i class="el-icon-view" style="cursor: pointer;" @click.stop="seeDetail(item)"></i>
                    </el-tooltip>
                  </el-col>
                  <el-col :span="8">
                    <span>
                      <i class="el-icon-time"></i>
                      <span style="font-size: 10px;">{{item.addTime.split(' ')[1].split(':')[0] + ':' + item.addTime.split(' ')[1].split(':')[1] || '-'}}</span>
                    </span>
                  </el-col>
                </el-row>
              </el-card>
            </el-popover>
        </el-col>
      </el-row>
    </div>
    <div v-else>
      <el-divider content-position="center">暂无数据</el-divider>
    </div>
    <!-- 详情弹窗 -->
    <el-dialog :title="detail ? detail.title : '-'" :visible.sync="showdialog" width="30%">
      <div style="display: flex; flex-direction: column; align-items: self-start;" v-show="detail">
        <span class="detail">名称: {{detail ? detail.title : '-'}}</span>
        <span class="detail">店铺名称: {{detail ? detail.shopName : '-'}}</span>
        <span class="detail">价格: {{detail ? detail.price : '-'}}</span>
        <span class="detail">商品地址: {{detail ? detail.url : '-'}}</span>
        <span class="detail">优惠券: {{detail ? detail.sales : '-'}}</span>
        <span class="detail">优惠券个数: {{detail ? detail.salesCnt : '-'}}</span>
        <span class="detail">上海是否有货: {{detail ? detail.shStore == 1 ? '有' : '无' : '-'}}</span>
        <span class="detail">北京是否有货: {{detail ? detail.bjStore == 1 ? '有' : '无' : '-'}}</span>
        <span class="detail">广州是否有货: {{detail ? detail.gzStore == 1 ? '有' : '无' : '-'}}</span>
        <span class="detail">杭州是否有货: {{detail ? detail.hzStore == 1 ? '有' : '无' : '-'}}</span>
        <span class="detail">评价总数: {{detail ? detail.commentCnt : '-'}}</span>
        <span class="detail">差评数量: {{detail ? detail.commentBadCnt : '-'}}</span>
        <span class="detail">图片评价数量: {{detail ? detail.commentPicCnt : '-'}}</span>
        <span class="detail">视频评价数量: {{detail ? detail.commentVideoCnt : '-'}}</span>
        <span class="detail">追加评价数量: {{detail ? detail.commentAppendCnt : '-'}}</span>
        <span class="detail">好评数量: {{detail ? detail.commentGoodCnt : '-'}}</span>
        <span class="detail">中评数量: {{detail ? detail.commentMiddleCnt : '-'}}</span>
        <span class="detail">上海发货单位: {{detail ? detail.shSend : '-'}}</span>
        <span class="detail">北京发货单位: {{detail ? detail.bjSend : '-'}}</span>
        <span class="detail">广州发货单位: {{detail ? detail.gzSend : '-'}}</span>
        <span class="detail">杭州发货单位: {{detail ? detail.hzSend : '-'}}</span>
        <span class="detail">数据日期: {{detail ? detail.dataDay : '-'}}</span>
        <span class="detail">数据时间: {{detail ? detail.updateTime : '-'}}</span>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showdialog = false">关 闭</el-button>
      </div>
    </el-dialog>
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
      detail: null,
      dataSource: [
      ],
      sortBytimeUp: true, // 按时间排序升序
      sortBypriceUp: false, // 按价格排序升序
      showdialog: false, // 显示详情弹窗
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
      let dataSource = JSON.parse(JSON.stringify(this.dataSource));
      if (dataSource.length > 0) {
        if (this.sortBytimeUp) { // 升序
          dataSource.map(r => ({
            appendList: r.appendList.sort((x, y) => new Date(x.addTime).getTime() - new Date(y.addTime).getTime())
          }))
          this.dataSource = dataSource;
        } else { // 降序
          dataSource.map(r => ({
            appendList: r.appendList.sort((x, y) => new Date(y.addTime).getTime() - new Date(x.addTime).getTime())
          }))
          this.dataSource = dataSource;
        }
      }
    },
    sortByprice() { // 按价格排序
      this.sortBypriceUp = !this.sortBypriceUp;
      let dataSource = JSON.parse(JSON.stringify(this.dataSource));
      if (dataSource.length > 0) {
        if (this.sortBypriceUp) { // 升序
          dataSource.map(r => ({
            appendList: r.appendList.sort((x, y) => x.price - y.price)
          }))
          this.dataSource = dataSource;
        } else { // 降序
          dataSource.map(r => ({
            appendList: r.appendList.sort((x, y) => y.price - x.price)
          }))
          this.dataSource = dataSource;
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
    },
    goLink(url) { // 点击跳转
      if (url) {
        window.open(url, "_blank");
      }
    },
    seeDetail(item) { // 查看详情
      this.detail = JSON.parse(JSON.stringify(item));
      this.showdialog = true;
    }
  }
}
</script>

<style scoped>
  .jing {
    width: 18px;
    height: 18px;
    font-size: 12px;
    line-height: 12px;
    margin-left: 10px;
    margin-bottom: 5px;
    color: white;
    font-weight: 500;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgb(211, 12, 12);
    border-radius: 35%;
  }
  .shang {
    width: 18px;
    height: 18px;
    font-size: 12px;
    line-height: 12px;
    margin-left: 10px;
    margin-bottom: 5px;
    color: white;
    font-weight: 500;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: orange;
    border-radius: 35%;
  }
  .right {
    float: right;
  }
  .content {
    background-color: rgb(182, 178, 178);
    padding: 10px;
  }
  .item {
    display: flex;
    margin-left: 10px;
    font-size: 14px;
    line-height: 17px;
  }
  .detail {
    display: flex;
    margin-left: 15px;
    font-size: 14px;
    line-height: 17px;
    margin-bottom: 6px;
    font-weight: 500;
  }
  .flex {
    display: flex;
    flex-direction: row;
    /* justify-content: space-between; */
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
  .down {
    color: #8a8a8a;
  }
  .el-divider--horizontal {
    margin: 10px 0;
  }
</style>
