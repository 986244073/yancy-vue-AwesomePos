<template>
  <div class="pos">
    <div>
      <el-row>
        <el-col :span='7' class="post-order" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table border style="width: 100%" :data="tableData">
                <el-table-column prop="goodsName" label="商品名称">
                </el-table-column>
                <el-table-column prop="count" label="数量" width="50">
                </el-table-column>
                <el-table-column prop="price" label="金额" width="70">
                </el-table-column>
                <el-table-column label="操作" width="100" fixed="right">
                  <template scope="scope">
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totalDiv">
                <small>数量</small>
                :{{totalCount}} &nbsp; &nbsp;&nbsp; &nbsp;
                <small>金额</small>
                : {{totalMoney}} 元
              </div>
              <div class="div-btn">
                <el-button type="warning">挂单</el-button>
                <el-button type="danger" @click="delAllGoods">删除</el-button>
                <el-button type="success" @click="checkout">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单"></el-tab-pane>
            <el-tab-pane label="外卖"></el-tab-pane>
          </el-tabs>
        </el-col>
        <!--我是产品栏-->
        <el-col :span="17" class="post-order1">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <div class="cookList">
                  <li v-for="goods in type0Goods" @click="addOrderList(goods)">
              <span class="foodImg">
              <img :src="goods.goodsImg" width="100%" alt="找不到图片嗷">
              </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </div>
              </el-tab-pane>
              <el-tab-pane label="小食">

                <div class="cookList">
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
              <span class="foodImg">
              <img :src="goods.goodsImg" width="100%" alt="找不到图片嗷">
              </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </div>
              </el-tab-pane>
              <el-tab-pane label="饮料">

                <div class="cookList">
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
              <span class="foodImg">
              <img :src="goods.goodsImg" width="100%" alt="找不到图片嗷">
              </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </div>
              </el-tab-pane>
              <el-tab-pane label="套餐">

                <div class="cookList">
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)">
              <span class="foodImg">
              <img :src="goods.goodsImg" width="100%" alt="找不到图片嗷">
              </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </div>
              </el-tab-pane>

            </el-tabs>
          </div>


        </el-col>
      </el-row>


    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: "Pos",
    created() {

      axios.get('https://www.easy-mock.com/mock/5d2ee51119eaf4229dd2b24a/awesomepos/oftenGoods')
        .then(response => {
          console.log(response);
          this.oftenGoods = response.data;
        }).catch(error => {
        console.log(error);
        alert('网络错误，不能访问');
      });
      axios.get('https://www.easy-mock.com/mock/5d2ee51119eaf4229dd2b24a/awesomepos/typeGoods')
        .then(response => {
          console.log(response);
          this.type0Goods = response.data[0];
          this.type1Goods = response.data[1];
          this.type2Goods = response.data[2];
          this.type3Goods = response.data[3];
        }).catch(error => {
        console.log(error);
        alert('网络错误，不能访问');
      });


    },
    data() {
      return {
        type0Goods: [],
        type1Goods: [],
        type2Goods: [],
        type3Goods: [],
        tableData: [],
        oftenGoods: [],
        totalMoney: 0,
        totalCount: 0
      }
    },
    methods: {
      //添加订单
      addOrderList(goods) {
        //判断商品在列表中
        this.totalMoney = 0;
        this.totalCount = 0;

        let isHave = false;
        for (let i = 0; i < this.tableData.length; i++) {
          if (this.tableData[i].goodsId === goods.goodsId) {
            isHave = true;
          }
        }
        //  编写逻辑
        if (isHave) {
          //  改变商品数量
          let arr = this.tableData.filter(o => o.goodsId === goods.goodsId);
          arr[0].count++;

        } else {
          let newGoods = {
            goodsId: goods.goodsId,
            goodsName: goods.goodsName,
            price: goods.price, count: 1
          };
          this.tableData.push(newGoods);
        }

        this.getAllMoney();

      },
      //删除单个商品
      delSingleGoods(goods) {
        this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
        this.getAllMoney();
      },
      //  获取全部金额
      getAllMoney() {
        this.totalCount = 0;
        this.totalMoney = 0;
        if (this.tableData) {
          //计算总价格
          this.tableData.forEach((element) => {
            this.totalCount += element.count;
            this.totalMoney = this.totalMoney + (element.price * element.count);
          })
        }
      },
      //删除全部商品
      delAllGoods() {
        this.tableData = [];
        this.totalMoney = 0;
        this.totalCount = 0;
      },
      //  模拟结账
      checkout() {
        if (this.totalCount !== 0) {
          this.tableData = [];
          this.totalMoney = 0;
          this.totalCount = 0;
          this.$message({
            message: '结账成功',
            type:'success'
          });
        }else {
          this.$message.error('请添加商品')
        }
      }
    },
    mounted() {
      let orderHeight = document.body.clientHeight;
      console.log(orderHeight);
      document.getElementById('order-list').style.height = orderHeight + 'px';
    }
  }
</script>

<style scoped>
  .pos {
    font-size: 12px;
  }

  .pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
  }

  .order-btn {
    margin-top: 10px;
    text-align: center;
  }

  .title {
    height: 20px;
    border-bottom: 1px solid #D3DCE6;
    background-color: #F9FAFC;
    padding: 10px;
  }

  .often-goods-list ul li {
    list-style: none;
    float: left;
    border: 1px solid #E5E9F2;
    padding: 10px;
    margin: 5px;
    background-color: #fff;
    cursor: pointer;
  }

  .goods-type {
    clear: both;
  }

  .o-price {
    color: #58B7FF;
  }

  .often-goods-list {
    border-bottom: 1px solid #C0CCDA;
    height: auto;
    overflow: hidden;
    padding-bottom: 10px;
    background-color: #F9FAFC;
  }

  .cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: auto;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;

  }

  .cookList li span {

    display: block;
    float: left;
  }

  .foodImg {
    width: 40%;
  }

  .foodName {
    font-size: 16px;
    padding-left: 10px;
    color: brown;

  }

  .foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
  }

  .totalDiv {
    background-color: #fff;
    padding: 10px;
    border-bottom: 1px solid #d3dce6;
  }
</style>
