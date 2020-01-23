<template>
  <div class="page" v-loading.fullscreen="loading" element-loading-background="rgba(0, 0, 0, 0.8)">
    <header>
      <h3><i class="el-icon-mic"></i>智能音乐平台</h3>
      <el-button type="primary" size=mini v-if="!username" @click="showLoginDialog">登录</el-button>
      <el-dropdown v-else @command="handleCommand">
        <span class="el-dropdown-link">
          {{username}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="a">我的收藏</el-dropdown-item>
          <el-dropdown-item command="b">后台管理</el-dropdown-item>
          <el-dropdown-item command="c">退出</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <div class="body">
      <div class="search-box">
        <el-input placeholder="请输入音乐专辑或者歌手" v-model="searchContent" class="input-with-select">
          <el-button slot="append" type="primary" icon="el-icon-search"></el-button>
        </el-input>
      </div>
      
    </div>
    <LoginDialog :loginDialogFormVisible="loginDialogFormVisible" @hideDialog="hideDialog" @successCallback="successCallback"></LoginDialog>
  </div>
</template>

<script>
import LoginDialog from './login';
export default {
  name: 'Index',
  components: {
    LoginDialog
  },
  data () {
    return {
      username: '',
      loginDialogFormVisible: false,
      loading: false,
      searchContent: ''
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
        case 'b': 
          
          break;
        case 'c': this.loginOut();break;
        default: break;
      }
    },
    loadingSwitch(flag) {
      this.loading = flag;
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
  .body {
    .search-box {
      width: 400px;
      margin: 40px auto;
    }
  }
}

</style>
