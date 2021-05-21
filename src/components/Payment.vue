<template>
  <div id="app-content">
		<section class="page-header page-header-xs">
		  <div class="container">
		    <h1>未支付的订单</h1>
		    <ol class="breadcrumb">
		      <li>
		        <a href="/">首页</a>
		      </li>
		      <li>未支付的订单</li>
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
						<span>{{scope.row.quantity}}</span><br>
						<span>￥{{scope.row.shopPrice/100}}</span>
					</template>
				 </el-table-column> 
				 
				 
				 <el-table-column label="总价">
				 	<template slot-scope="scope">
						<span>￥{{scope.row.shopPrice/100 * scope.row.quantity}}</span>
					</template>
				 </el-table-column>
			 </el-table>
			 
			 <div id="contact" style="margin-top: 30px;">
				 <h4>请选择联系人</h4>
				 <li style="list-style: none;">
					 <div v-for="item in contactList" :key="item">
						<el-radio @change="selectC(item.id,item.name,item.mobile,item.address)" v-model="radio" :label="item.id">
							姓名：{{item.name}},电话:{{item.mobile}},地址：{{item.address}}
						</el-radio>
					 </div>
				 </li>
			 </div>
			 <div style="margin-top: 30px;">
				 <h4>请选择联系方式</h4>
				 <img @click="payNow" src="../../static/pc_wxqrpay.png">
				 <img @click="payNow" src="../../static/alipaypcnew.png">
				 <a>电子钱包</a>
			 </div>
			 
			 
		 </div>
		 
		 
		 
		 <div id="price">
			 <h2>商品总价：{{allPrice}}</h2>
			 <h2>邮寄费用：包邮</h2>
			 <h2>合计总价：{{allPrice}}</h2>
		 </div>
		 
	 </div>
  </div>
</template>

<script>
	export default{
		data(){
			return{
				selectId:null,
				selectName:null,
				selectMobile:null,
				selectAddress:null,
				radio:null,
				contactList:null,
				allPrice:null,
				cartList:{
					productList:[]
				}
			}
		},
		created() {
			this.createLocal()
			this.getCartList()
			this.getContactList()
		},
		methods:{
			createLocal(){
				var order = {
					orderNum:'',
					selectId:'',
					payStatus:'',
					allPrice:'',
				}
				localStorage.setItem('order',JSON.stringify(order))
			},
			getContactList(){
				var vm = this
				vm.$post(vm.API.API_URL_CONTECT,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.contactList =  res.data.data
				})
			},
			getCartList(){
				var vm = this
				vm.$post(vm.API.API_URL_CART,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.cartList = res.data
					vm.allPrice = vm.cartList.price/100
				})
			},
			selectC(id,name,mobile,address){
				var vm = this
				vm.selectId = id
				vm.selectName = name
				vm.selectMobile = mobile
				vm.selectAddress = address
			},
			payNow(){
				var vm = this
				if(vm.cartList.productList.length == 0){
					vm.$message.warning('暂无商品')
				}
				else if(vm.selectId == null){
					vm.$message.warning('请选择联系人')
				}
				else{
					
					var order = JSON.parse(localStorage.getItem('order'))
					order.orderNum = vm.API.API_getOrderNum
					order.selectId = vm.selectId
					order.allPrice = vm.allPrice
					localStorage.setItem('order',JSON.stringify(order))
					console.log(vm.API.API_getDateTime)
					debugger
					
					vm.$post(vm.API.API_URL_ADDORDER,{
						orderNum:vm.API.API_getOrderNum,
						price:vm.allPrice*100,
						paymentFlag:0,
						userId:vm.Storage.Local.get('User').id,
						contactName:vm.selectName,
						contactMobile:vm.selectMobile,
						contactAddress:vm.selectAddress,
						status:1,
						type:0,
						createTime:vm.API.API_getDateTime
					}).then(function(res){
						if(res.data == 1){
							vm.$router.push('/qr')
						}
					})
					
					
					
				}
			}
		}
	}
</script>

<style scoped>
</style>
