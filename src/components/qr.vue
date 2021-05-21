<template>
	<div id="wapper">
		<div id="content" style="text-align: center;margin-top: 300px;margin-bottom: 500px;">
			<h1>订单已生产，请在{{time}}秒之内支付订单</h1>
			<h2>订单编号：{{ordernum}}</h2>
			<vue-qr :text="content"></vue-qr>
		</div>
	</div>
</template>

<script>
	import VueQr from 'vue-qr'
	export default{
		components:{VueQr},
		data(){
			return{
				time:30,
				content:null,
			}
			
		},
		created() {
			this.getData()
			this.checkStatus()
		},
		methods:{
			// 清空购物车
			deleAll(){
				var vm = this
				vm.$post(vm.API.API_URL_CART_ITEMS_REMOVEALL,{
					userId:vm.Storage.Local.get('User').id
				})
			},
			// 从浏览器中拿到数据
			getData(){
				var vm = this
				vm.ordernum = JSON.parse(localStorage.getItem('order')).orderNum
				var userid = vm.Storage.Local.get('User').id
				var selectid = JSON.parse(localStorage.getItem('order')).selectId
				
				
				vm.content = '{"orderNum":"'+vm.ordernum+'","userId":"'+userid+'","id":"'+selectid+'","paymentType":"1"}'
			},
			// 检查订单是否支付
			checkStatus(){
				var vm = this
				
				var check = setInterval(function(){
					vm.$post(vm.API.API_CHECK_ORDER,{
						orderNum:vm.ordernum
					}).then(function(res){
						if(res.data == 1){
							clearInterval(check)
							clearTimeout(timeOut)
							clearInterval(seconds)
							vm.deleAll()
							var order = JSON.parse(localStorage.getItem('order'))
							order.payStatus = true
							localStorage.setItem('order',JSON.stringify(order))
							
							vm.$message.success('支付中，请稍等')
							setTimeout(function(){
								vm.$router.push('/account/paymentResult')
							},2000)
						}
					})
				},500)
				
				var seconds = setInterval(function(){
					vm.time --
					if(vm.time == 0){
						clearInterval(seconds)
					}
				},1000)
				
				
				var timeOut = setTimeout(function(){
					vm.$message.warning('超时未支付')
					vm.$router.push('/account/payment')
				},30000)
				
				
			},
		}
	}
</script>

<style>
</style>
