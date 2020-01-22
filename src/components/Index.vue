<template>
  <div class="page">
    <header>
      <h3><i class="el-icon-mic"></i>智能音乐平台</h3>
      <el-button type="primary" size=mini v-if="!username" @click="showLoginDialog">登录</el-button>
      <el-dropdown v-else @command="handleCommand">
        <span class="el-dropdown-link">
          {{username}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="a">我的收藏</el-dropdown-item>
          <el-dropdown-item command="b">退出</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <LoginDialog :loginDialogFormVisible="loginDialogFormVisible" @hideDialog="hideDialog" @successCallback="successCallback"></LoginDialog>
  </div>
</template>

<script>
import LoginDialog from './login';
export default {
  name: 'HelloWorld',
  components: {
    LoginDialog
  },
  data () {
    return {
      username: '',
      loginDialogFormVisible: false
    }
  },
  beforeMount() {
    // 从sessionStorage中读取个人信息
    let username = sessionStorage.getItem('username');
    if (username) {
      this.username = username;
    }
  },
  methods: {
    showLoginDialog() {
      this.loginDialogFormVisible = true;
    },
    hideDialog() {
      this.loginDialogFormVisible = false;
    },
    successCallback(username) {
      this.username = username;
    },
    loginOut() {
      this.username = '';
      sessionStorage.removeItem('username');
    },
    handleCommand(command) {
      switch(command){
        case 'a': break;
        case 'b': this.loginOut();break;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.page {
  header {
    display: flex;
    align-items: center;
    height: 40px;
    border-bottom: 1px solid #ddd;
    padding: 0 20px;

    .el-dropdown-link {
      cursor: pointer;
      color: #409EFF;
    }
    .el-icon-arrow-down {
      font-size: 12px;
    }

    & > h3 {
      flex: 1;
    }
    
  }
}

</style>
