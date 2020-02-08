<template>
  <div class="page" v-loading.fullscreen="loading" element-loading-background="rgba(0, 0, 0, 0.8)">
    <header>
      <h3><i class="el-icon-mic"></i>智能音乐平台后台管理系统</h3>
    </header>
    <div class="body">
      <div class="search-box-wrap" :class="{'search-show-result': showAlbumsResults}">
        <div class="search-box">
          <el-input placeholder="请输入音乐专辑或者歌手,为空时则检索所有专辑和歌手" v-model="searchContent" class="input-with-select">
            <el-button slot="append" type="primary" icon="el-icon-search" @click="search"></el-button>
          </el-input>
        </div>
      </div>
      
      <div class="table-wrap" v-if="showAlbumsResults">
        <div class="table-header-wrap">
            <p>专辑列表</p>
            <div class="handle-wrap">
                <el-button type="primary" size="mini" @click="addAlbumBtnClick">添加专辑</el-button>
                <el-button type="success" size="mini" @click="initAlbumsDataBtnClick">初始化专辑数据</el-button>
            </div>
        </div>
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
              <el-button @click="updateAlbumBtnClick(scope.row)" type="text" size="small">修改</el-button>
              <el-button @click="removeAlbumBtnClick(scope.row)" type="text" size="small">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <span v-else>未检索到相应信息</span>
      </div>
      <div class="table-wrap" v-if="showSingersResults">
        <div class="table-header-wrap">
            <p>歌手列表</p>
            <div class="handle-wrap">
                <el-button type="primary" size="mini" @click="addSingerBtnClick">添加歌手</el-button>
                <el-button type="success" size="mini" @click="initSingersDataBtnClick">初始化歌手数据</el-button>
            </div>
        </div>
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
          <el-table-column
            label="操作"
            width="100">
            <template slot-scope="scope">
              <el-button @click="updateSingerBtnClick(scope.row)" type="text" size="small">修改</el-button>
              <el-button @click="removeSingerBtnClick(scope.row)" type="text" size="small">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <span v-else>未检索到相应信息</span>
      </div>
    </div>
    <AlbumDialog :type="albumType" :album="currentAlbum" v-if="showAlbumDialog" @hideDialog="hideDialog" @confirmAlbumDialog="confirmAlbumDialog"></AlbumDialog>
  </div>
</template>

<script>
import AlbumDialog from '../components/addAlbumDialog';
export default {
  name: 'Index',
  components: {
    AlbumDialog
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
      showAlbumsResults: false,
      albumType: 'add',
      showAlbumDialog: false,
      currentAlbum: null
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
    hideDialog(type) {
      if (type === 'album') {
        this.showAlbumDialog = false;
      } else if (type === 'singer') {
      }
    },
    confirmAlbumDialog(album) {
      let url = '';
      if (this.albumType === 'add') {
        url = this.baseUrl + '/albums/add';
      } else {
        url = tihs.baseUrl + '/albums/update'
      }
      fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(album), // "{"name":"Billy","age":18}"
      }).then(response => response.json()).then(response => {
          if (response.code == 0) {
            if (this.albumType === 'add') {
              this.albums.push(response.album);
            } else {
              
            }
          }
        this.hideDialog('album');
      })
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
          this.albums = response.map(album => {
            album.handle = '收藏';
            return album;
          })
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
    addAlbumBtnClick() {
      this.albumType = 'add';
      this.showAlbumDialog = true;
    },
    initAlbumsDataBtnClick() {

    },
    updateAlbumBtnClick(data) {
      this.currentAlbum = data;
      this.albumType = 'update';
      this.showAlbumDialog = true;
    },
    removeAlbumBtnClick(data) {

    },
    addSingerBtnClick() {

    },
    initSingersDataBtnClick() {

    },
    updateSingerBtnClick(data) {

    },
    removeSingerBtnClick(data) {

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.page {
  height: 100%;
  overflow: auto;
  header {
    display: flex;
    align-items: center;
    height: 50px;
    border-bottom: 1px solid #ddd;
    padding: 0 20px;
    background-color: #409EFF;
    color: #fff;

    & > h3 {
      flex: 1;
      margin: 0;
      padding: 0;
      .el-icon-mic {
        margin-right: 5px;
        font-size: 22px;
      }
    }
    
    
  }
  .body {
    height: calc(100% - 51px);
    overflow: auto;
    .search-box-wrap {
      position: relative;
      height: 100%;
      transition: height linear .5s;

      &.search-show-result {
        height: 150px;

        .search-box {
          top: 50%;
        }
      }


      .search-box {
        width: 700px;
        position: absolute;
        left: 50%;
        top: 40%;
        transform: translate(-50%, -50%);
        transition: top linear 0.3s;

        /deep/ .el-input__inner {
          border: 1px solid #409EFF;
          height: 60px;
          line-height: 60px;
          font-size: 16px;
        }

        /deep/ .el-input-group__append {
          border: 1px solid #409EFF;
          border-left: 0;
          background-color: #409EFF;
          color: #fff;
          font-size: 20px;
        }
      }
    }
    
    .table-wrap {
      padding: 0 30px;
      margin-bottom: 30px;

      .table-header-wrap {
        display: flex;
        align-items: center;
        border-bottom: 2px solid #666;
        padding-bottom: 5px;


        & > p {
            flex: 1;
            font-weight: 700;
            margin: 0;
        }

      }
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

      
      & > span {
        font-size: 14px;
      }
    }
    
  }
}

</style>
