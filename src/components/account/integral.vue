<template>
	<div id="wapper" style="margin-left: 100px;">
		<section class="page-header page-header-xs">
		  <div class="container">
		    <h1>个人中心</h1>
		
		    <!-- breadcrumbs -->
		    <el-breadcrumb separator-class="el-icon-arrow-right">
		      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
		      <el-breadcrumb-item>个人中心</el-breadcrumb-item>
		      <el-breadcrumb-item>常用联系人</el-breadcrumb-item>
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
			  <li>
			   <a  @click="goto('/account/footPrint','footPrint')">我的足迹</a>
			  </li>
				<li class="active">
				 <a  @click="goto('/account/integral','integral')">我的积分</a>
				</li>
		    </ul>
		    <!-- /page tabs -->
		  </div>
		</section>
		<div class="container">
			
			
		  <div class="panel panel-default" style="margin-top: 30px;">
		    <div class="panel-heading">
		      <h2 class="panel-title">我的消费记录</h2>
			  <h2>总积分：{{parseInt(allJifen/1000)}}</h2>
		    </div>
		
		    <table class="table contact">
		      <thead>
		        <tr>
		          <th>订单编号</th>
		          <th>订单金额</th>
		          <th>支付时间</th>
		          <th>订单积分</th>
		        </tr>
		      </thead>
		      <tbody>
		        <template>
		          <tr v-for="item in payHistoryList" :key="item">
		            <td>{{item.orderNum}}</td>
		            <td>￥{{item.price/100}}</td>
		            <td>{{item.createTime}}</td>
		            <td>
						<span v-if="item.price/100>10">
							{{parseInt(item.price/1000)}}
						</span>
						<span v-else>
							订单金额小于十元，无法获得积分
						</span>
					</td>
		          </tr>
		        </template>
		      </tbody>
		    </table>
			
		    
		  </div>
			
			<div class="block" style="margin-bottom: 50px;">
				
				<el-pagination
				      :page-size="20"
					  @current-change="handleCurrentChange"
					  :current-page="nowPage"
				      layout=" prev, pager, next"
				      :total="maxPage">
				    </el-pagination>
			 <!-- <el-pagination
			    layout="prev, pager, next"
			    :total="maxPage*10">
			  </el-pagination> -->
			</div>
		</div>
		
		
	</div>
</template>

<script>
	export default{
		name:"integral",
		data(){
			return{
				payHistoryList:null,
				allJifen:null,
				maxPage:null,
				nowPage:1
			}
		},
		created() {
			this.getData()
		},
		methods:{
			// 获取基础数据
			getData(){
				var vm = this
				vm.$post(vm.API.API_URL_FIND_JIFEN,{
					userId:vm.Storage.Local.get('User').id
				}).then(function(res){
					vm.payHistoryList =  res.data
					vm.allJifen = res.data[0].allPrice
					vm.maxPage = res.data.length
					
				})
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
