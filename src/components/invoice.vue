<template>
	<div id="wapper" style="margin-bottom: 800px;">
		
		
		<div v-if="orderHaving!=null">
			<section class="page-header page-header-xs">
			  <div class="container">
			    <h1>填写发票信息</h1>
			    <ol class="breadcrumb">
			      <li>
			        <a href="/">首页</a>
			      </li>
			      <li>添加发票</li>
			    </ol>
			  </div>
			</section>
			<div id="content" style="margin:  80px 30px;">
				<h3>当前订单编号:{{orderNum}}</h3>
				<h3>订单金额：{{price}}</h3>
			</div>
			
			<div style="width: 1500px;height: 500px;border: 1px solid gray;margin: 30px 30px;">
				<div style="margin: 50px 50px ;">
					<el-radio v-model="radio" label="0">增值税专用发票</el-radio>
					<el-radio v-model="radio" label="1">普通发票</el-radio>
				</div>
				<div style="display: flex;justify-content: space-around;">
					<div>单位名称：<el-input v-model="name" style="width: 300px;"></el-input></div>
					<div>受理银行：<el-input v-model="bank" style="width: 300px;"></el-input></div>
					<div>银行账号：<el-input v-model="account" style="width: 300px;"></el-input></div><br>
				</div>
				
				<div style="display: flex;justify-content: space-around;margin-top: 50px;">
					<div>单位地址：<el-input v-model="address" style="width: 300px;"></el-input></div>
					<div>单位电话：<el-input v-model="phone" style="width: 300px;"></el-input></div>
					<div>备注：<el-input style="width: 300px;"></el-input></div><br>
				</div>
				
				<div style="padding: 50px 50px;">
					<el-button @click="goHome" type="danger">算了，不想开了</el-button>
					<el-button @click="addInvoice" type="success">提交信息</el-button>
				</div>
			</div>
		</div>
		
		<div style="margin: 30px 30px;">
			<h1>暂无订单信息,无法进行发票操作</h1>
		</div>
		
		
		
	</div>
</template>

<script>
	export default{
		name:'addInvoice',
		data(){
			return{
				orderHaving:null,
				name:null,
				bank:null,
				account:null,
				address:null,
				phone:null,
				radio:null,
				price:null,
				orderNum:null,
			}
		},
		created() {
			this.getdata()
		},
		methods:{
			// 获取订单信息
			getdata(){
				var vm = this
				var order = JSON.parse(localStorage.getItem('order'))
				if(order.orderNum != null){
					vm.orderNum = order.orderNum
					vm.price = order.allPrice
					vm.orderHaving = 1
				}
			},
			goHome(){
				var vm = this
				localStorage.setItem('order','')
				vm.$router.push('/')
			},
			// 添加发票
			addInvoice(){
				var vm = this
				if(vm.radio == null){
					vm.$message.warning('请选择发票类型')
				}
				else if(vm.name == null||vm.bank == null||vm.account == null||vm.address == null||vm.phone == null){
					vm.$message.warning('请填写完整')
					
				}
				else{
					
					vm.$post(vm.API.API_URL_ADD_INVOICE,{
						userId:vm.Storage.Local.get('User').id,
						receName:vm.name,
						consId:'003',
						bank:vm.bank,
						bankAccount:vm.account,
						address:vm.address,
						phone:vm.phone,
						createTime:vm.API.API_getDateTime,
						type:vm.radio
					}).then(function(res){
						if(res.data ==1){
							vm.$message.success('提交发票成功，两秒后跳转到发票历史')
							localStorage.setItem('order','')
							setTimeout(function(){
								vm.$router.push('/account/invoice')
							},2000)
						}
					})
				}
				
			}
		}
	}
</script>

<style>
</style>
