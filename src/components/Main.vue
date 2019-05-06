<template>
  <div
    v-loading="loading"
    element-loading-text="正在加载中..."
    element-loading-spinner="el-icon-loading">
    <el-form :inline="true" :model="formInline" style="margin-top: 30px; margin-bottom: -10px;">
      <el-form-item label="标 签">
        <el-select v-model="formInline.region" placeholder="请选择标签">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="品 牌">
        <el-select v-model="formInline.region" placeholder="请选择品牌">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="分 组">
        <el-select v-model="formInline.region" placeholder="请选择分组">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="search">查 询</el-button>
      </el-form-item>
    </el-form>
    <el-divider><i class="el-icon-search"></i></el-divider>
    <div class="content">
      <el-row>
        <el-col>
          <el-button-group class="right">
            <el-button size="mini" @click="sortBytime">添加时间<i :class="{'el-icon--right': true, 'el-icon-sort-up': sortBytimeUp, 'el-icon-sort-down': !sortBytimeUp}"></i></el-button>
            <el-button size="mini" @click="sortByprice">价格<i :class="{'el-icon--right': true, 'el-icon-sort-up': sortBypriceUp, 'el-icon-sort-down': !sortBypriceUp}"></i></el-button>
          </el-button-group>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col v-for="(item, index) in dataSource" :key="index" :span="3">
            <el-popover
              placement="right-start"
              width="200"
              trigger="hover">
              <div>
                <p>满150元包邮</p>
                <p>满1000元减10元</p>
                <p>满1999元减20元</p>
              </div>
              <el-card shadow="hover" style="margin-top: 10px;" slot="reference" :body-style="{ padding: '10px 0px 0px 0px' }">
                <div class="item">小米官网</div>
                <br />
                <div :class="['item', 'price', {'hight': index > 4, 'mid': index < 6, 'low': index > 9}]">
                  <img src="../assets/tickt.png" width="30" height="30" alt="" srcset="" v-if="index < 6">
                  <img src="../assets/low.png" width="30" height="30" alt="" srcset="" v-else>
                  <span style="margin-left: 5px;">¥10000</span>
                </div>
                <br />
                <div class="item discount">优惠券数量: 3</div>
                <br />
                <div class="item flex">
                  <el-badge :value="200" class="item" type="warning">
                  </el-badge>
                  <span><i class="el-icon-time"></i>11:30</span>
                </div>
              </el-card>
            </el-popover>
        </el-col>
      </el-row>
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
      formInline: {
        name: '',
        region: ''
      },
      dataSource: [
        {id: 1, name: 'zhangsan1'},
        {id: 2, name: 'zhangsan2'},
        {id: 3, name: 'zhangsan3'},
        {id: 4, name: 'zhangsan4'},
        {id: 5, name: 'zhangsan5'},
        {id: 6, name: 'zhangsan6'},
        {id: 7, name: 'zhangsan7'},
        {id: 8, name: 'zhangsan8'},
        {id: 9, name: 'zhangsan9'},
        {id: 10, name: 'zhangsan10'},
        {id: 11, name: 'zhangsan11'}
      ],
      sortBytimeUp: true, // 按时间排序升序
      sortBypriceUp: false // 按价格排序升序
    }
  },
  mounted() { // 初始化
    // axios.all([
    //   axios.get('https://api.github.com/xxx/1'),
    //   axios.get('https://api.github.com/xxx/2')
    // ])
    // .then(axios.spread(function (userResp, reposResp) {
    //   // 上面两个请求都完成后，才执行这个回调方法
    //   console.log('User', userResp.data);
    //   console.log('Repositories', reposResp.data);
    // }));
    http.post('http://47.97.252.72:8080/mrsm/tstype.do?getTypeDetail', {
      typegroupid: '2c5e5c466a687b54016a6cddef1d0065'
    }).then(res => {
      console.log(res);
    })
    setTimeout(() => {
      this.loading = false;
    }, 3000)
  },
  methods: {
    search() { // 查询
      console.log('search');
    },
    sortBytime() { // 按时间排序
      this.sortBytimeUp = !this.sortBytimeUp;
    },
    sortByprice() { // 按价格排序
      this.sortBypriceUp = !this.sortBypriceUp;
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
    line-height: 15px;
  }
  .flex {
    display: flex;
    flex-direction: row;
    justify-content:space-between;
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
