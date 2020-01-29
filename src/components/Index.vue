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
          <!-- <el-dropdown-item command="b">后台管理</el-dropdown-item> -->
          <el-dropdown-item command="c">退出</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <div class="body">
      <div class="search-box">
        <el-input placeholder="请输入音乐专辑或者歌手" v-model="searchContent" class="input-with-select">
          <el-button slot="append" type="primary" icon="el-icon-search" @click="search"></el-button>
        </el-input>
      </div>
      <div class="table-wrap" v-if="showAlbumsResults">
        <p>专辑列表</p>
        <el-table
          :data="albums"
          style="width: 100%"
          v-if="albums.length">
          <el-table-column
            prop="address"
            label="封面">
            <template slot-scope="item">
              <div class="img-wrap">
                <img :src="item.row.cover">
              </div>
            </template>
          </el-table-column>
          <el-table-column
            prop="album_id"
            label="专辑ID"
            width="180">
          </el-table-column>
          <el-table-column
            prop="album_name"
            label="专辑名称"
            width="180">
          </el-table-column>
          <el-table-column
            prop="public_time"
            label="发行时间">
          </el-table-column>
          <el-table-column
            prop="price"
            label="价格">
          </el-table-column>
          <el-table-column
            fixed="right"
            label="操作"
            width="100">
            <template slot-scope="scope">
              <el-button @click="collect(scope.row)" type="text" size="small">收藏</el-button>
            </template>
          </el-table-column>
        </el-table>
        <span v-else>未检索到相应信息</span>
      </div>
      <div class="table-wrap" v-if="showSingersResults">
        <p>歌手列表</p>
        <el-table
          :data="singers"
          style="width: 100%"
          v-if="singers.length">
          <el-table-column
            prop="singer_id"
            label="歌手ID"
            width="180">
          </el-table-column>
          <el-table-column
            prop="singer_name"
            label="歌手姓名"
            width="180">
          </el-table-column>
        </el-table>
        <span v-else>未检索到相应信息</span>
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
      baseUrl: 'http://localhost:3000',
      username: '',
      loginDialogFormVisible: false,
      loading: false,
      searchContent: '',
      albums: [],
      singers: [],
      showSingersResults: false,
      showAlbumsResults: false
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
    },
    search() {
      this.searchAlbums();
      this.searchSingers();
    },
    searchAlbums() {
      let url = '';
      if (!this.searchContent) {
        url = '/albums/';
        
      } else {
        url = '/albums/getAlbumsByName?name=' + this.searchContent
      }
      fetch(this.baseUrl + url).then(response => response.json()).then(response => {
          this.albums = response;
          this.showAlbumsResults = true;
      })
    },
    searchSingers() {
      let url = '';
      if (!this.searchContent) {
        url = '/singers/';
        
      } else {
        url = '/singers/getSingersByName?name=' + this.searchContent
      }
      fetch(this.baseUrl + url).then(response => response.json()).then(response => {
          this.singers = response;
          this.showSingersResults = true;
      })
    },
    collect(data) {
      let userId = sessionStorage.getItem('userId');
      let url = `${this.baseUrl}/users/collect`;
      let params = {
        albumsId: data['_id'],
        userId: userId
      }
      fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(params), 
      })
      .then(res => res.json())
      .then(res => {
        if (res.code == 0) {
          this.$message({
            showClose: true,
            message: '收藏成功',
            type: 'success'
          });
        }
      })
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
    .table-wrap {
      padding: 0 30px;
      margin-bottom: 30px;
      .img-wrap {
        width: 30px;
        height: 60px;
        display: flex;
        align-items: center;
        justify-content: center;
        
        img {
          max-width: 100%;
          max-height: 100%;
        }
      }

      & > p {
        border-bottom: 2px solid #666;
        padding-bottom: 15px;
        font-weight: 700;
      }

      & > span {
        font-size: 14px;
      }
    }
    
  }
}

</style>
