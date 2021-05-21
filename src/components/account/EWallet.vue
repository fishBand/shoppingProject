<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>充值</el-breadcrumb-item>
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
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li>
            <a @click="goto('/account/setting','UserSetting')">个人设置</a>
          </li>
          <li class="active">
            <a>充值</a>
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
    <section>
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">账户充值</h2>
          </div>
          <br>
          <div class="row">
            <h4 class="text-center">当前电子钱包账户余额为：{{userMoney}}</h4>
          </div>
          <div class="panel-body">
            <fieldset class="margin-top-10">
              <div class="row">
                <div class="form-group col-lg-12">
                  <label for="account">
                    请输入充值金额
                    <span style="color:red">*</span>
                  </label>
                  <el-input
					v-model="inpuntMoney"
					
                    type="text"
                    class="required"
                    placeholder="请输入充值金额"
                    maxlength="255"
                  />
                  <div class="erro">{{msg}}</div>
                </div>
              </div>
              <el-button
			  @click="addMoney"
                type="primary"
                class="pull-right margin-top-20"
                icon="el-icon-circle-plus-outline"
              >充值</el-button>
            </fieldset>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "EWallet",
  data() {
    return {
			userMoney:null,
			inpuntMoney:'',
      msg: null
    };
  },
  created() {
  	this.getMoney()
  },
  methods: {
    // 获取钱包
		getMoney(){
			var vm = this
			vm.$post(vm.API.API_URL_CUSTOMER_Account,{
				userId:vm.Storage.Local.get('User').id
			}).then(function(res){
				if(res.errorCode == 0){
					vm.userMoney = res.data.amount
				}
			})
		},
    // 钱包充值
		addMoney(){
			var vm = this
			var reRu = /^[0-9]*$/
			var re = new RegExp(reRu)
			if(vm.inpuntMoney == ''){
				vm.$message.warning('请填写金额')
			}
			else if(!re.test(vm.inpuntMoney)){
				vm.$message.warning('请正确填写金额')
			}
			else{
				vm.$post(vm.API.API_URL_CUSTOMER_Recharge_Account,{
					userId:vm.Storage.Local.get('User').id,
					amount:vm.inpuntMoney
				}).then(function(res){
					if(res.data == true){
						vm.$message.success('充值成功')
						
					}
				})
			}
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

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
