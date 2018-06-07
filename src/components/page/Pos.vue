<template>
  <div class="pos">
     <el-row>
       <el-col :span='6' class="pos-order" id="order-list">
          <el-tabs class="first-div">
             <el-tab-pane label="点餐" >
                <el-table :data="tableData" border>
                  <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                  <el-table-column prop="count" label="数量"></el-table-column>
                  <el-table-column prop="price" label="金额"></el-table-column>
                  <el-table-column  label="操作" fixed="right">
                    <template slot-scope="scope">
                      <el-button type="text" size="small" @click="addOrderList(scope.row)"><i class="el-icon-circle-plus-outline"></i></el-button>
                      <el-button type="text" size="small" @click="delSingleGoods(scope.row)"><i class="el-icon-circle-close-outline"></i></el-button>
                    </template>
                  </el-table-column>
                </el-table>
                <div class="totalNumPri">
                   <small class="padding-n-p">数量:<big>{{totalCount}}</big></small>
                   <small class="padding-n-p">总价:<big>￥{{totalMoney}}</big></small>
                </div>
                
                <div class="div-btn">
                  <el-button type="warning">挂单</el-button>
                  <el-button type="danger" @click="delAllGoods()">删除</el-button>
                  <el-button type="success" @click="checkout()">结账</el-button>
                </div>
             </el-tab-pane>
             <el-tab-pane label="挂单">
                
             </el-tab-pane>
             <el-tab-pane label="外卖">
                
             </el-tab-pane>
          </el-tabs>
       </el-col>
       <!--高频率商品布局 -->
       <el-col :span='16'>
         <div class="often-goods">
            <div class="title">热门商品</div>
            <div class="often-goods-list">
              <ul>
                 <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                   <span>{{goods.goodsName}}</span>
                   <span class="o-price">￥{{goods.price}}元</span>
                 </li>

              </ul>
            </div>
         </div>
 
        <!--主食、小食、饮料、套餐 -->
        <div class="goods-type">
           <el-tabs>
             <el-tab-pane label="汉堡">
               <div>
                 <ul class="cookList">
                   <li v-for="goods in type0Goods "  @click="addOrderList(goods)">
                     <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                     <span class="foodName">{{goods.goodsName}}</span>
                     <span class="foodPrice">￥{{goods.price}}.00元</span> 
                   </li>
                 </ul>
               </div>
             </el-tab-pane>
             <el-tab-pane label="小食">
               <div>
                 <ul class="cookList">
                   <li v-for="goods in type1Goods "  @click="addOrderList(goods)">
                     <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                     <span class="foodName">{{goods.goodsName}}</span>
                     <span class="foodPrice">￥{{goods.price}}.00元</span> 
                   </li>
                 </ul>
               </div>
             </el-tab-pane>
             <el-tab-pane label="饮料">
               <div>
                 <ul class="cookList">
                   <li v-for="goods in type2Goods "  @click="addOrderList(goods)">
                     <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                     <span class="foodName">{{goods.goodsName}}</span>
                     <span class="foodPrice">￥{{goods.price}}.00元</span> 
                   </li>
                 </ul>
               </div>
             </el-tab-pane>
             <el-tab-pane label="套餐">
               <div>
                 <ul class="cookList">
                   <li v-for="goods in type3Goods "  @click="addOrderList(goods)">
                     <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                     <span class="foodName">{{goods.goodsName}}</span>
                     <span class="foodPrice">￥{{goods.price}}.00元</span> 
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
import axios from 'axios'; 
export default {
  name:'pos',
  data(){
    return{
      tableData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalMoney:0,
      totalCount:0
    }
  },
  created:function(){
     axios.get('http://jspang.com/DemoApi/oftenGoods.php')
     .then(response=>{
        //console.log(response);
        this.oftenGoods=response.data;
     }).catch(erro=>{
       // console.log(erro)
       alert('抱歉!erro...')
     }),

       axios.get('http://jspang.com/DemoApi/typeGoods.php')
       .then(response=>{
        this.type0Goods=response.data[0];
        this.type1Goods=response.data[1];
        this.type2Goods=response.data[2];
        this.type3Goods=response.data[3];
     }).catch(erro=>{
       // console.log(erro)
       alert('抱歉!erro...')
     })
      
  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;       //获取当前页面的高度
    document.getElementById('order-list').style.height=orderHeight+'px';
  },

  methods:{
    addOrderList(goods){
      
       //商品是否存在订单列表中
       let isHave=false;
       for(let i=0;i<this.tableData.length;i++){
         if(this.tableData[i].goodsId==goods.goodsId){
           isHave=true;
         }
       }
       //根据判断的值编写业务逻辑
       if(isHave){
         //改变列表中商品的数量
         let arr=this.tableData.filter(a=>a.goodsId==goods.goodsId);
         arr[0].count++;
         arr[0].price+=(arr[0].price)/((arr[0].count)-1);
       }else{
         let newGoods={goodsId:goods.goodsId, goodsName:goods.goodsName,price:goods.price,count:1};
         this.tableData.push(newGoods);
       }
      
      this.getAllCountMoney();
       },

    delSingleGoods(goods){
        this.tableData=this.tableData.filter(a=>a.goodsId!=goods.goodsId);
        this.getAllCountMoney();
    },
    
    delAllGoods(){
      this.tableData=[];
      this.totalMoney=0;
      this.totalCount=0;
    },
    
    //汇总数量和价格
    getAllCountMoney(){
       this.totalMoney=0;
       this.totalCount=0;
       if(this.tableData){
         this.tableData.forEach((val)=>{
         this.totalCount+=val.count;
         this.totalMoney+=val.price;
        })
       }
    },
    checkout(){
       if(this.totalCount!=0){
         this.tableData=[];
         this.totalMoney=0;
         this.totalCount=0;
         this.$message({
           message:'结账成功，请到前台领取你的食物！',
           type:'success'
         });
       }else{
          this.$message.error('请选购商品！');
       }

    }
    
   }

}
</script>

<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;  
}
.first-div{
  margin-left: 10px;
  margin-right: 10px;
}
.div-btn{
  margin-top: 10px;
}
.title{
  height: 20px;
  border-bottom: 1px solid #c0ccda;
  background-color:#f9fafc;
  text-align:left;
  padding: 9px;
}
.often-goods-list ul li{
    list-style: none;
    float: left;
    border: 1px solid #c0ccda;
    padding: 10px;
    margin: 10px;
    background-color:#f9fafc;
    font-size: 15px;
    cursor: pointer;
}
.o-price{
  color: #58b7ff;
}
.goods-type{
  clear: both;
  padding: 9px
}

.cookList li{
       list-style: none;
       width:18%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 5px;
       cursor: pointer;
 
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 14px;
       padding-left: 10px;
       color:brown;
       margin-top: 3px;
 
   }
   .foodPrice{
       font-size: 14px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalNumPri{
     color:#909399;
     border-bottom:1px solid #d3dce6;
     padding: 10px;
     background-color: #ffffff;
   }
   .padding-n-p{
     padding:15px;
   }
</style>
