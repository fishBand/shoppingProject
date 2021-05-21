<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>我的发票</el-breadcrumb-item>
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
			<li class="active">
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
            <h2 class="panel-title">我的发票记录</h2>
          </div>

          <table class="table contact">
            <thead>
              <tr>
                <th>单位名称</th>
                <th>受理银行</th>
                <th>银行账号</th>
                <th>单位地址</th>
                <th>单位电话</th>
                <th>发票类型</th>
				<th>创建时间</th>
                <th>发票状态</th>
              </tr>
            </thead>
            <tbody>
              <template v-if="invoiceList!=null&&invoiceList.legnth!=0">
                <tr v-for="item in invoiceList" :key="item">
                  <td>{{item.receName}}</td>
                  <td>{{item.bank}}</td>
                  <td>{{item.bankAccount}}</td>
                  <td>{{item.address}}</td>
                  <td>{{item.phone}}</td>
                  <td>
					  <span v-if="item.type == 1">增值发票</span>
					  <span v-else>普通发票</span>
				  </td>
				  <td>{{item.createTime}}</td>
				  <td>
					  <span v-if="item.status == 1">已开票</span>
					  <span v-else>未开票</span>
				  </td>
                </tr>
              </template>
            </tbody>
          </table>
          
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "Invoice",
  data() {
    return {
		invoiceList:null,
      accountList: null,
      name: null,
      mobile: null,
      address: null,
      editFlag: false,
      editId: null,
      tips: {
        nameMsg: "",
        mobileMsg: "",
        addressMsg: ""
      }
    };
  },
  created() {
	  this.getInvoiceList()
  },
  methods: {
    // 获取发票列表
	  getInvoiceList(){
      var vm = this
      vm.$post(vm.API.API_URL_FIND_INVOICE,{
        userId:vm.Storage.Local.get('User').id
      }).then(function(res){
        vm.invoiceList = res.data
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
.contact {
  margin-top: 0px !important;
  border-bottom: 1px solid #ddd;
}
.erro,
.required {
  color: red;
}
</style>
