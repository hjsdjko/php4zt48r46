<template>
  <div>
    <div class="container" :style='{"minHeight":"100vh","alignItems":"flex-start","background":"url(http://codegen.caihongy.cn/20230929/d93b0147c674432baaffb7336caae57c.jpg)","display":"flex","width":"100%","backgroundSize":"100% 100%","backgroundPosition":"center center","backgroundRepeat":"no-repeat","justifyContent":"flex-end"}'>
      <el-form :style='{"border":"0px solid #f6f6f6","padding":"20px 20px","margin":"0","alignItems":"flex-start","textAlign":"center","flexDirection":"column","display":"flex","minHeight":"100vh","borderRadius":"20px 0 0 20px","flexWrap":"wrap","background":"rgba(255,255,255,1)","width":"50vw","fontSize":"14px","height":"auto"}'>
        <div v-if="true" :style='{"padding":"0px","margin":"20px auto 40px","borderColor":"#ddd","color":"#769589","textAlign":"center","display":"inline-block","background":"none","borderWidth":"0px","width":"100%","lineHeight":"40px","fontSize":"30px","borderStyle":"solid","fontWeight":"600"}' class="title-container">基于PHP的减脂轻食购物网站的设计与实现登录</div>
        <div v-if="loginType==1" class="list-item" :style='{"width":"60%","margin":"0 auto 20px","position":"relative","alignItems":"center","flexWrap":"wrap","display":"flex"}'>
          <div v-if="true" class="lable" :style='{"color":"#666","textAlign":"right","left":"-150px","width":"150px","lineHeight":"44px","fontSize":"inherit","position":"absolute"}'>用户名：</div>
          <input :style='{"border":"0px solid #eee","padding":"0 10px","boxShadow":"0 2px 4px #769589","outline":"0px solid #eee","color":"#666","outlineOffset":"0px","borderRadius":"30px","background":"#fff","width":"100%","fontSize":"inherit","height":"50px"}' placeholder="请输入用户名" name="username" type="text" v-model="rulesForm.username">
        </div>
        <div v-if="loginType==1" class="list-item" :style='{"width":"60%","margin":"0 auto 20px","position":"relative","alignItems":"center","flexWrap":"wrap","display":"flex"}'>
          <div v-if="true" class="lable" :style='{"color":"#666","textAlign":"right","left":"-150px","width":"150px","lineHeight":"44px","fontSize":"inherit","position":"absolute"}'>密码：</div>
          <input :style='{"border":"0px solid #eee","padding":"0 10px","boxShadow":"0 2px 4px #769589","outline":"0px solid #eee","color":"#666","outlineOffset":"0px","borderRadius":"30px","background":"#fff","width":"100%","fontSize":"inherit","height":"50px"}' placeholder="请输入密码" name="password" type="password" v-model="rulesForm.password">
        </div>

        <div :style='{"width":"60%","margin":"20px auto","fontSize":"inherit","textAlign":"left","order":"10"}' v-if="roles.length>1" prop="loginInRole" class="list-type">
          <el-radio v-for="item in roles" v-bind:key="item.roleName" v-model="rulesForm.role" :label="item.roleName">{{item.roleName}}</el-radio>
        </div>

		
        <div :style='{"margin":"20px auto 0","alignItems":"center","flexWrap":"wrap","display":"flex","width":"60%","fontSize":"16px","position":"relative","justifyContent":"center"}'>
          <el-button v-if="loginType==1" :style='{"border":"0","cursor":"pointer","padding":"0px","margin":"0 auto 20px","color":"#fff","textAlign":"center","outline":"none","borderRadius":"30px","background":"#769589","width":"100%","fontSize":"16px","fontWeight":"600","height":"50px","order":"2"}' type="primary" @click="login()" class="loginInBt">登录</el-button>
        </div>
      </el-form>

    </div>
  </div>
</template>
<script>
import menu from "@/utils/menu";
export default {
  data() {
    return {
		verifyCheck2: false,
		flag: false,
      baseUrl:this.$base.url,
      loginType: 1,
      rulesForm: {
        username: "",
        password: "",
        role: "",
      },
      menus: [],
      roles: [],
      tableName: "",
    };
  },
  mounted() {
    let menus = menu.list();
    this.menus = menus;

    for (let i = 0; i < this.menus.length; i++) {
      if (this.menus[i].hasBackLogin=='是') {
        this.roles.push(this.menus[i])
      }
    }

  },
  created() {

  },
  destroyed() {
	    },
  components: {
  },
  methods: {

    //注册
    register(tableName){
		this.$storage.set("loginTable", tableName);
		this.$router.push({path:'/register',query:{pageFlag:'register'}})
    },
    // 登陆
    login() {

		if (!this.rulesForm.username) {
			this.$message.error("请输入用户名");
			return;
		}
		if (!this.rulesForm.password) {
			this.$message.error("请输入密码");
			return;
		}
		if(this.roles.length>1) {
			if (!this.rulesForm.role) {
				this.$message.error("请选择角色");
				return;
			}

			let menus = this.menus;
			for (let i = 0; i < menus.length; i++) {
				if (menus[i].roleName == this.rulesForm.role) {
					this.tableName = menus[i].tableName;
				}
			}
		} else {
			this.tableName = this.roles[0].tableName;
			this.rulesForm.role = this.roles[0].roleName;
		}
		
		this.loginPost()
    },
	loginPost() {
		this.$http({
			url: `${this.tableName}/login?username=${this.rulesForm.username}&password=${this.rulesForm.password}`,
			method: "post"
		}).then(({ data }) => {
			if (data && data.code === 0) {
				this.$storage.set("Token", data.token);
				this.$storage.set("role", this.rulesForm.role);
				this.$storage.set("sessionTable", this.tableName);
				this.$storage.set("adminName", this.rulesForm.username);
				this.$router.replace({ path: "/" });
			} else {
				this.$message.error(data.msg);
			}
		});
	},
  }
}
</script>

<style lang="scss" scoped>
.container {
  min-height: 100vh;
  position: relative;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover;
      background: url(http://codegen.caihongy.cn/20230929/d93b0147c674432baaffb7336caae57c.jpg);
        
  .list-item /deep/ .el-input .el-input__inner {
		border: 0px solid #eee;
		border-radius: 30px;
		padding: 0 10px;
		box-shadow: 0 2px 4px #769589;
		outline: 0px solid #eee;
		color: #666;
		background: #fff;
		width: 100%;
		font-size: inherit;
		outline-offset: 0px;
		height: 50px;
	  }
  
  .list-item.select /deep/ .el-select .el-input__inner {
		border: 1px solid #eee;
		border-radius: 0px;
		padding: 0 10px;
		color: #666;
		width: 276px;
		font-size: 14px;
		height: 40px;
	  }
  
  .list-code /deep/ .el-input .el-input__inner {
  	  	border: 0px solid #eee;
  	  	border-radius: 30px 0 0 30px;
  	  	padding: 0 10px;
  	  	outline: none;
  	  	color: #666;
  	  	background: none;
  	  	width: calc(100% - 100px);
  	  	font-size: inherit;
  	  	height: 50px;
  	  }

  .list-type /deep/ .el-radio__input .el-radio__inner {
		border-radius: 2px;
		background: rgba(53, 53, 53, 0);
		border-color: #666666;
	  }
  .list-type /deep/ .el-radio__input.is-checked .el-radio__inner {
        border-radius: 2px;
        background: #000;
        border-color: #000;
      }
  .list-type /deep/ .el-radio__label {
		color: #666666;
		font-size: 16px;
	  }
  .list-type /deep/ .el-radio__input.is-checked+.el-radio__label {
        color: #000;
        font-size: 16px;
      }
}

</style>
