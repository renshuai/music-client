<template>
  <div class="page" v-loading.fullscreen="loading" element-loading-background="rgba(255, 255, 255, 0.8)">
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
    <AlbumDialog :baseUrl="baseUrl" :type="albumType" :album="currentAlbum" v-if="showAlbumDialog" @hideDialog="hideDialog" @confirmAlbumDialog="confirmAlbumDialog"></AlbumDialog>
    <SingerDialog :type="singerType" :singer="currentSinger" v-if="showSingerDialog" @hideDialog="hideDialog" @confirmSingerDialog="confirmSingerDialog"></SingerDialog>
  </div>
</template>

<script>
import AlbumDialog from '../components/addAlbumDialog';
import SingerDialog from '../components/addSingerDialog';
export default {
  name: 'Index',
  components: {
    AlbumDialog,
    SingerDialog
  },
  data () {
    return {
      baseUrl: 'http://106.13.136.196:3000',
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
      currentAlbum: null,
      singerType: 'add',
      showSingerDialog: false,
      currentSinger: null
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
        this.showSingerDialog = false;
      }
    },
    confirmAlbumDialog(album) {
      this.loadingSwitch(true);
      let url = '';
      if (this.albumType === 'add') {
        url = this.baseUrl + '/albums/add';
      } else {
        url = this.baseUrl + '/albums/update'
      }
      fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(album), // "{"name":"Billy","age":18}"
      }).then(response => response.json()).then(response => {
        this.loadingSwitch(false);
        if (response.code == 0) {
          if (this.albumType === 'add') {
            this.albums.push(response.album);
            this.$message({
              showClose: true,
              message: response.msg,
              type: 'success'
            });
          } else {
            this.$message({
              showClose: true,
              message: response.msg,
              type: 'success'
            });
          }
        }
        this.hideDialog('album');
      })
    },
    confirmSingerDialog(singer) {
      this.loadingSwitch(true);
      let url = '';
      if (this.singerType === 'add') {
        url = this.baseUrl + '/singers/add';
      } else {
        url = this.baseUrl + '/singers/update'
      }
      fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(singer), // "{"name":"Billy","age":18}"
      }).then(response => response.json()).then(response => {
        this.loadingSwitch(false);
        if (response.code == 0) {
          if (this.singerType === 'add') {
            this.singers.push(response.singer);
            this.$message({
              showClose: true,
              message: response.msg,
              type: 'success'
            });
          } else {
            this.$message({
              showClose: true,
              message: response.msg,
              type: 'success'
            });
          }
        }
        this.hideDialog('singer');
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
      this.loadingSwitch(true);
      let url = '';
      if (!this.searchContent) {
        url = '/albums/';
        
      } else {
        url = '/albums/getAlbumsByName?name=' + this.searchContent
      }
      fetch(this.baseUrl + url).then(response => response.json()).then(response => {
        this.loadingSwitch(false);
          this.albums = response.map(album => {
            album.handle = '收藏';
            return album;
          })
          this.showAlbumsResults = true;
      })
    },
    searchSingers() {
      this.loadingSwitch(true);
      let url = '';
      if (!this.searchContent) {
        url = '/singers/';
        
      } else {
        url = '/singers/getSingersByName?name=' + this.searchContent
      }
      fetch(this.baseUrl + url).then(response => response.json()).then(response => {
        this.loadingSwitch(false);
          this.singers = response;
          this.showSingersResults = true;
      })
    },
    addAlbumBtnClick() {
      this.albumType = 'add';
      this.showAlbumDialog = true;
    },
    initAlbumsDataBtnClick() {
      this.$confirm('确定要初始化专辑数据吗', '专辑数据初始化', {
          confirmButtonText: '确定',
          cancelButtonText: '取消'
      }).then(() => {
        this.loadingSwitch(true);
        let url = `${this.baseUrl}/albums/init`;
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          }
        }).then(response => response.json()).then(response => {
          this.loadingSwitch(false);
            if (response.code == 0) {
              this.albums = response.albums;
              this.$message({
                showClose: true,
                message: response.msg,
                type: 'success'
              });
            } else {
              this.$message({
                showClose: true,
                message: response.msg,
                type: 'error'
              });
            }
        })
      }).catch(() => {
        console.log('init cancel');
      })
    },
    updateAlbumBtnClick(data) {
      this.currentAlbum = data;
      this.albumType = 'update';
      this.showAlbumDialog = true;
    },
    removeAlbumBtnClick(data) {
      this.$confirm('确定要删除该专辑吗', '专辑删除', {
          confirmButtonText: '确定',
          cancelButtonText: '取消'
      }).then(() => {
        this.loadingSwitch(true);
          let options = {
              method: 'DELETE',//post请求
              headers: {
                  'Accept': 'application/json',
                  'Content-Type': 'application/json'
              }
          }
          fetch(`${this.baseUrl}/albums/${data._id}`, options).then(res => {
              return res.json();
          }).then(json => {
            this.loadingSwitch(false);
            if (json.code == 0) {
              let index=this.albums.findIndex(item=>item._id==data._id);
              this.albums.splice(index, 1);
              this.$message({
                showClose: true,
                message: json.msg,
                type: 'success'
              });
            } else {
              this.$message({
                showClose: true,
                message: json.msg,
                type: 'error'
              });
            }
              
          }).catch(err => {
              alert(err);
          })
      }).catch(() => {
        this.loadingSwitch(false);
          console.log('已取消删除');
      });

    },
    addSingerBtnClick() {
      this.singerType = 'add';
      this.showSingerDialog = true;
    },
    initSingersDataBtnClick() {
      this.$confirm('确定要初始化歌手数据吗', '歌手数据初始化', {
          confirmButtonText: '确定',
          cancelButtonText: '取消'
      }).then(() => {
        this.loadingSwitch(true);
        let url = `${this.baseUrl}/singers/init`;
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          }
        }).then(response => response.json()).then(response => {
          this.loadingSwitch(false);
            if (response.code == 0) {
              this.singers = response.singers;
              this.$message({
                showClose: true,
                message: response.msg,
                type: 'success'
              });
            } else {
              this.$message({
                showClose: true,
                message: response.msg,
                type: 'error'
              });
            }
        })
      }).catch(() => {
        this.loadingSwitch(false);
        console.log('init cancel');
      })
    },
    updateSingerBtnClick(data) {
      this.currentSinger = data;
      this.singerType = 'update';
      this.showSingerDialog = true;
    },
    removeSingerBtnClick(data) {
      this.$confirm('确定要删除该歌手吗', '歌手删除', {
          confirmButtonText: '确定',
          cancelButtonText: '取消'
      }).then(() => {
        this.loadingSwitch(true);
          let options = {
              method: 'DELETE',//post请求
              headers: {
                  'Accept': 'application/json',
                  'Content-Type': 'application/json'
              }
          }
          fetch(`${this.baseUrl}/singers/${data._id}`, options).then(res => {
              return res.json();
          }).then(json => {
            this.loadingSwitch(false);
            if (json.code == 0) {
              let index=this.singers.findIndex(item=>item._id==data._id);
              this.singers.splice(index, 1);
              this.$message({
                showClose: true,
                message: json.msg,
                type: 'success'
              });
            } else {
              this.$message({
                showClose: true,
                message: json.msg,
                type: 'error'
              });
            }
              
          }).catch(err => {
              alert(err);
          })
      }).catch(() => {
        this.loadingSwitch(false);
          console.log('已取消删除');
      });
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
