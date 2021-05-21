<template>
  <div id="app-content">
		<section class="page-header page-header-xs">
		  <div class="container">
		    <h1>购物车</h1>
		    <ol class="breadcrumb">
		      <li>
		        <a href="/">首页</a>
		      </li>
		      <li>购物车</li>
		    </ol>
		  </div>
		</section>
   <div id="wapper" style="display: flex;justify-content: space-around">
		 <div id="cart">
			 <el-table :data="cartList.productList" style="width: 1200px;">
				 <el-table-column label="商品信息" width="500">
					 <template slot-scope="scope">
						 <img :src="API.BASE_SERVER_URL+scope.row.defaultImg" width="45" height="45"><br>
						 <span>{{scope.row.name}}</span>
					 </template>
				 </el-table-column>
				 
				 <el-table-column label="数量">
				 	<template slot-scope="scope">
						<el-input-number @change="changePrice" v-model="scope.row.quantity"></el-input-number><br>
						<span>￥{{scope.row.shopPrice/100}}</span>
					</template>
				 </el-table-column> 
				 
				 
				 <el-table-column label="总价">
				 	<template slot-scope="scope">
						<span>￥{{scope.row.shopPrice/100 * scope.row.quantity}}</span>
					</template>
				 </el-table-column>
				 
				 
				 <el-table-column label="操作">
				 	<template slot-scope="scope">
						<el-button @click="deleOne(scope.row.id)" type="text">删除</el-button>
					</template>
				 </el-table-column>
			 </el-table>
			 <div style="padding: 30px 30px;">
				 <el-button @click="deleAll" type="danger">清空购物车</el-button>
				 <el-button @click="udateAll" type="primary">更新购物车</el-button>
			 </div>
			 
		 </div>
		 
		 
		 
		 <div id="price">
			 <h2>商品总价：{{allPrice}}</h2>
			 <h2>邮寄费用：包邮</h2>
			 <h2>合计总价：{{allPrice}}</h2>
			 <el-button @click="payNow" type="success">立即支付</el-button>
		 </div>
		 
	 </div>
  </div>
</template>

<script>
	export default{
		data(){
			return{
				allPrice:null,
				cartList:{
					productList:[]
				}
			}
		},
		created() {
			this.getCartList()
		},
		methods:{
			// 商品数量变更时，更改购物车总价
			changePrice(){
				var vm = this
				this.allPrice = 0
				for(var i = 0;i<vm.cartList.productList.length;i++){
					vm.allPrice += vm.cartList.productList[i].shopPrice/100 * vm.cartList.productList[i].quantity
				}
			},
			// 获取购物车列表
			getCartList(){
				var vm = this
				vm.$post(vm.API.API_URL_CART,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.cartList = res.data
					vm.allPrice = vm.cartList.price/100
				})
			},
			// 清空购物车
			deleAll(){
				var vm = this
				vm.$post(vm.API.API_URL_CART_ITEMS_REMOVEALL,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					if(res.errorCode == 0){
						vm.$message.success('清空成功')
						vm.getCartList()
					}
				})
			},
			// 删除某个商品
			deleOne(proid){
				var vm = this
				vm.$post(vm.API.API_URL_CART_ITEMS_REMOVE,{
					productId:proid,
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					if(res.errorCode == 0){
						vm.$message.success('删除成功')
						vm.getCartList()
					}
				})
			},
			// 保存购物车更新
			udateAll(){
			  var vm = this;
			  var user = vm.Storage.Local.get("User");
			  if (user) {
				var userId = user.id;
				var cartItemList = [];
				var cartItem = {};
				var cartList = this.cartList.productList;
				for (var cart in cartList) {
				  var cartItem = {};
				  cartItem["productId"] = cartList[cart].id;
				  cartItem["quantity"] = cartList[cart].quantity;
				  cartItem["skuId"] = cartList.skuId;
				  cartItem["optionValueKeyGroup"] = cartItem.optionValueKeyGroup;
				  cartItemList.push(cartItem);
				}
			  }
			  vm.$post(vm.API.API_URL_CART_ITEMS_UPDATE, {
				userId: userId,
				cartItemList: cartItemList
			  }).then(res => {
				if (res.errorCode == 0 && res.data == 1) {
				  vm.getCartList();
				  vm.Toastr.success("更新成功！");
				} else {
				  vm.Toastr.error("更新失败！");
				}
			  });
			},
			// 点击立即支付
			payNow(){
				var vm = this
				if(vm.cartList.productList.length == 0){
					vm.$message.warning('暂无商品')
				}
				else{
					vm.$router.push('/account/payment')
				}
			}
		}
	}
</script>

<style scoped>
</style>
