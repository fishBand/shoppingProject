<template>
	<div id="wapper" style="margin-left: 100px;">
		<section class="page-header page-header-xs">
		  <div class="container">
		    <h1>个人中心</h1>
		
		    <!-- breadcrumbs -->
		    <el-breadcrumb separator-class="el-icon-arrow-right">
		      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
		      <el-breadcrumb-item>个人中心</el-breadcrumb-item>
		      <el-breadcrumb-item>我的足迹</el-breadcrumb-item>
		    </el-breadcrumb>
		    <!-- /breadcrumbs -->
		
		    <!-- page tabs -->
		    <ul class="page-header-tabs list-inline">
		      <li>
		        <a @click="goto('/account/order/list','OrderList')">我的订单</a>
		      </li>
		      <li>
		        <a>常用联系人</a>
		      </li>
		      <li>
		        <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
		      </li>
		      <li>
		        <a @click="goto('/account/setting','UserSetting')">个人设置</a>
		      </li>
		      <li>
		        <a @click="goto('/account/ewallet','EWallet')">充值</a>
		      </li>
		      <li>
		        <a  @click="goto('/account/payment','Payment')">未支付订单</a>
		      </li>
				<li>
		       <a  @click="goto('/account/invoice','Invoice')">我的发票</a>
		      </li>
			  <li class="active">
			   <a  @click="goto('/account/footPrint','footPrint')">我的足迹</a>
			  </li>
			  <li>
			   <a  @click="goto('/account/integral','integral')">我的积分</a>
			  </li>
		    </ul>
		    <!-- /page tabs -->
		  </div>
		</section>
		<div id="content">
			<div id="today" style="width: 1500px;margin-top: 30px;">
				<h3>今日{{dateNow}}</h3>
				<h3>{{todayCount}}件宝贝</h3>
				<ul style="overflow: hidden;">
					<li style="list-style: none;">
						<div v-for="item in todayList" :key="item" style="float: left;width: 300px;height: 300px;border: 0.5px solid gray;margin-left: 30px;padding: 0px 30px;margin-top: 30px;;position: relative;" >
							<img @click="showPro(item.productId)" :src="API.BASE_SERVER_URL+item.defaultImg" width="200" height="200"><br>     
							<a @click="showPro(item.productId)">{{item.name}}</a><br>
							<span style="color: red;">￥{{item.shopPrice/100}}</span>
							<img @click="deleFootTo(item.productId)" style="position:absolute ;right: 20px;top: 20px;" src="../../../static/images/patterns/none.jpg">
						</div>
					</li>
				</ul>
				<hr>
			</div>
			
			
			<div id="history">
				<h3>历史足迹</h3>
				<h3>{{historyCount}}件宝贝</h3>
				<ul style="overflow: hidden;">
					<li style="list-style: none;">
						<div v-for="item in historyList" :key="item" style="float: left;width: 300px;height: 300px;border: 0.5px solid gray;margin-left: 30px;padding: 0px 30px;position: relative;" >
							<img @click="showPro(item.productId)" :src="API.BASE_SERVER_URL+item.defaultImg" width="200" height="200"><br>     
							<a @click="showPro(item.productId)">{{item.name}}</a><br>
							<span style="color: red;">￥{{item.shopPrice/100}}</span>
							<img @click="deleFootHi(item.productId)" style="position:absolute ;right: 20px;top: 20px;" src="../../../static/images/patterns/none.jpg">
						</div>
					</li>
				</ul>
				<hr>
			</div>
			
			
			
		</div>
		
		
	</div>
</template>

<script>
	export default{
		name:"FootPrint",
		data(){
			return{
				dateNow:null,
				todayList:null,
				todayCount:null,
				historyList:null,
				historyCount:null,
			}
		},
		created() {
			this.getToday();
			this.getHistory()
		},
		methods:{
			// 获取今日足迹
			getToday(){
				var vm = this
				vm.dateNow = vm.API.API_getDate
				vm.$post(vm.API.API_URL_FIND_FOOTTODYA,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.todayList = res.data
					vm.todayCount = res.data.length
				})
			},
			// 获取历史足迹
			getHistory(){
				var vm = this
				vm.$post(vm.API.API_URL_FIND_FOOTNOTTODAY,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.historyList = res.data
					vm.historyCount = res.data.length
				})
			},
			// 删除今日记录
			deleFootTo(proid){
				var vm = this
				vm.$post(vm.API.API_URL_DELE_FOOTTODAY,{
					userId:vm.Storage.Local.get('User').id,
					productId:proid
				}).then(function(res){
					if(res.errorCode == 0){
						vm.$message.success('删除成功')
						vm.getHistory()
						vm.getToday()
					}
				})
			},
			// 删除历史足迹
			deleFootHi(id){
				var vm = this
				vm.$post(vm.API.API_URL_DELE_FOOTHISTORY,{
					userId:vm.Storage.Local.get('User').id,
					productId:id
				}).then(function(res){
					if(res.errorCode == 0){
						vm.$message.success('删除成功')
						vm.getHistory()
						vm.getToday()
					}
				})
			},
			// 点击商品跳转
			showPro(id){
				var vm = this
				localStorage.setItem('proid',id)
				vm.$router.push('/product')
			},
			goto(path, name, val) {
			  this.$router.push({
			    path: path,
			    name: name
			  });
			}
		}
	}
</script>

<style>
</style>
