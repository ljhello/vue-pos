<template>
   <div class="pos">
       <el-row>
         <el-col :span='7' class="posmoney" id="order-list">
            <el-tabs>
                <el-tab-pane label="点餐">
                    <el-table border style="width:100%" :data='tabledata'>
                        <el-table-column label="商品名称" prop="goodsName" width="150"></el-table-column>
                        <el-table-column label="数量" prop="count" width='60'></el-table-column>
                        <el-table-column label="金额" prop="price" width='70'></el-table-column>
                        <el-table-column label="操作"  fixed='right'>
                            <template scope="scope">
                                <el-button type="text" size="small" @click="delsinglegood(scope.row)">删除</el-button>
                                <el-button type="text" size="small" @click="addgoodslist(scope.row)">增加</el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                    <div class="totaldiv">
                        <small>数量:</small>   {{totalpoint}} 件  &nbsp;  &nbsp;&nbsp;&nbsp; <small>价格:</small> {{totalmoney}} 元
                    </div>
                    <div class="div-btn">
                        <el-button type="warning">挂单</el-button>
                        <el-button type="danger" @click="delallgoods()">删除</el-button>
                        <el-button type="success" @click="checkout()">结账</el-button>
                    </div>

                </el-tab-pane>
                <el-tab-pane label="挂单">
                    挂单
                </el-tab-pane>
                <el-tab-pane label="外卖">
                    外卖
                </el-tab-pane>
            </el-tabs>
          </el-col>

          <el-col :span='15'>
             <div>
                 <div class="title">常用商品</div>
                 <div>
                     <ul class="goods-list">
                         <li v-for="list in oftengoods" @click="addgoodslist(list)">
                             <span>{{list.goodsName}}</span>
                             <span class="o-price">￥{{list.price}}元</span>
                         </li>
                     </ul>
                 </div>
             </div>
             
           <div class="goods-type">
             <el-tabs>
                 <el-tab-pane label="汉堡">
                       <div>
                           <ul class="cookList">
                               <li v-for="list in type0Goods" @click="addgoodslist(list)">
                                <span class="first">
                                    <img :src="list.goodsImg" alt="" class="foodImg">
                                </span>
                                <div class="foodName">{{list.goodsName}}</div> 
                                <div class="foodPrice">￥{{list.price}}元</div>
                               </li>
                           </ul>
                       </div>
                 </el-tab-pane>
                 <el-tab-pane label="小食">
                        <div>
                           <ul class="cookList">
                               <li v-for="list in type1Goods" @click="addgoodslist(list)">
                                <span class="first">
                                    <img :src="list.goodsImg" alt="" class="foodImg">
                                </span>
                                <div class="foodName">{{list.goodsName}}</div> 
                                <div class="foodPrice">￥{{list.price}}元</div>
                               </li>
                           </ul>
                       </div> 
                 </el-tab-pane>
                 <el-tab-pane label="饮料">
                          <div>
                           <ul class="cookList">
                               <li v-for="list in type2Goods"  @click="addgoodslist(list)">
                                <span class="first">
                                    <img :src="list.goodsImg" alt="" class="foodImg">
                                </span>
                                <div class="foodName">{{list.goodsName}}</div> 
                                <div class="foodPrice">￥{{list.price}}元</div>
                               </li>
                           </ul>
                       </div>
                 </el-tab-pane>
                 <el-tab-pane label="套餐">
                       <div>
                           <ul class="cookList">
                               <li v-for="list in type3Goods"  @click="addgoodslist(list)">
                                <span class="first">
                                    <img :src="list.goodsImg" alt="" class="foodImg">
                                </span>
                                <div class="foodName">{{list.goodsName}}</div> 
                                <div class="foodPrice">￥{{list.price}}元</div>
                               </li>
                           </ul>
                       </div>
                 </el-tab-pane>
             </el-tabs>

           </div>

          </el-col>

       </el-row>
 



   </div>
</template>
<script>
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tabledata: [],
      oftengoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalmoney: 0,
      totalpoint: 0
    };
  },
  created: function() {
    var url = "http://jspang.com/DemoApi/oftenGoods.php";
    axios
      .get(url)
      .then(res => {
        //   console.log(this);
        this.oftengoods = res.data;
      })
      .catch(error => {
        alert("网络错误，不能访问");
      });

    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(res => {
        this.type0Goods = res.data[0];
        this.type1Goods = res.data[1];
        this.type2Goods = res.data[2];
        this.type3Goods = res.data[3];
      })
      .catch(error => {
        alert("网络错误，不能访问");
      });
  },
  mounted: function() {
    var orderheight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderheight + "px";
  },
  methods: {
    addgoodslist(goods) {
      //商品是否存在在列表当中

      let ishave = false;
      for (let i = 0; i < this.tabledata.length; i++) {
        if (this.tabledata[i].goodsId == goods.goodsId) {
          ishave = true;
        }
      }

      //根据判断的值编写业务逻辑
      if (ishave) {
        let arr = this.tabledata.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newgoodarr = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tabledata.push(newgoodarr);
      }
       
       this.totalpomoney();

    },
    delsinglegood(goods) {
        
      this.tabledata= this.tabledata.filter(o => o.goodsId != goods.goodsId);
      this.totalpomoney();
    },
    delallgoods(){
       this.tabledata=[];
       this.totalmoney=0;
       this.totalpoint=0;
    },
    checkout(){
      if(this.totalmoney!=0){
         this.tabledata=[];
         this.totalmoney=0;
         this.totalpoint=0;
         this.$message({
             message:'结账成功，祝你用餐愉快!',
             type:'success'
         })
      }else{
          this.$message.error('请选择商品后结账！')
      }
    },
    totalpomoney() {
      this.totalmoney = 0;
      this.totalpoint = 0;
      if (this.tabledata) {
        this.tabledata.forEach(ele => {
          this.totalpoint += ele.count;
          this.totalmoney = this.totalmoney + ele.count * ele.price;
        });
      }
    }

    
  }
};
</script>
<style scope>
.pos {
  box-sizing: border-box;
}
.posmoney {
  box-sizing: border-box;
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 15px;
  text-align: center;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #e9fafc;
  padding: 10px;
  font-size: 16px;
}
.goods-list li {
  list-style: none;
  padding: 10px;
  margin: 10px;
  float: left;
  cursor: pointer;
  background-color: #fff;
  border: 1px solid #e5e9f2;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
  padding: 5px;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auto;
  overflow: hidden;
  cursor: pointer;
  background-color: #fff;
  padding: 4px;
  float: left;
  margin: 6px;
}
.cookList li span {
  display: block;
  float: left;
}
.cookList li .first {
  width: 50px;
}
.foodImg {
  width: 100%;
}
.foodName {
  font-size: 18px;
  padding-left: 60px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 60px;
  padding-top: 10px;
}
.totaldiv {
  text-align: center;
  color: palevioletred;
}
</style>
