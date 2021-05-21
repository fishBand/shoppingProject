<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->

        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>我的收藏</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">
          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li>
            <a @click="goto('/account/contact/list','ContactList')">常用联系人</a>
          </li>
          <li class="active">
            <a>我的收藏</a>
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
					<li>
					 <a  @click="goto('/account/integral','integral')">我的积分</a>
					</li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <section style="padding-top: 25px">
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">个人收藏</h2>
          </div>
		  <div>
			  <el-table :data="wishList">
				  <el-table-column label="商品介绍" width="500">
					  <template slot-scope="scope">
						  <img :src="API.BASE_SERVER_URL+scope.row.product.defaultImg" width="45" height="45"><br>
						  <span>{{scope.row.product.name}}</span>
					  </template>
				  </el-table-column>
				  
				  <el-table-column label="市场价">
				  		<template slot-scope="scope">
							<span>￥{{scope.row.product.price/100}}</span>
						</template>
				  </el-table-column>
				  
				  
				  <el-table-column label="店内价">
				  		<template slot-scope="scope">
							<span>￥{{scope.row.product.shopPrice/100}}</span>
						</template>
				  </el-table-column>
				  
				  
				  <el-table-column label="操作">
				  		<template slot-scope="scope">
							<el-button @click="changePro(scope.row.product.id)" type="text">查看商品</el-button>
							<el-button @click="deleWish(scope.row.id)" type="text">删除收藏</el-button>
						</template>
				  </el-table-column>
				  
			  </el-table>
		  </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
	data(){
		return{
			wishList:null,
		}
	},
	created() {
			this.getWishList()
	},
  methods: {
	  // 获取收藏列表
		getWishList(){
			var vm = this
			vm.$post(vm.API.API_URL_CUSTOMER_WISHLIST+'/1',{
				userId:vm.Storage.Local.get('User').id
			}).then(function(res){
				console.log(res)
				if(res.errorCode == 0){
					vm.wishList = res.data.wishlist
				}
			})
		},
	  changePro(proid){
		var vm = this
		localStorage.setItem('proid',proid)
		vm.$router.push('/product')
	  },
	  // 删除收藏
	  deleWish(id){
		  var vm = this
		  vm.$post(vm.API.API_URL_CUSTOMER_REMOVE_WISHLIST,{
			  id:id,
			  userId:vm.Storage.Local.get('User').id
		  }).then(function(res){
			  if(res.errorCode == 0){
				vm.$message.success('删除成功')
				vm.getWishList()
			  }
		  })
	  },
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    }
  }
};
</script>

<style scoped>
.table > tbody > tr > td {
  vertical-align: inherit;
}
</style>
