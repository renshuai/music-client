<template>
    <el-dialog :title="title" :visible="true" :before-close="hideDialog">
        <div v-if="currentAlbum" class="detail-block">
            <div class="detail-property-block upload-block" v-if="type === 'add'">
                <span>封面</span>
                <input type="file"  class="fileInput" ref="fileInput" accept="image/*"/>
                <el-button type="primary" size="mini" @click="upload">上传</el-button>
            </div>
            <div class="detail-property-block">
                <span>专辑ID</span>
                <el-input v-model="currentAlbum['album_id']" placeholder="请输入专辑ID"></el-input>
            </div>
            <div class="detail-property-block">
                <span>专辑名称</span>
                <el-input v-model="currentAlbum['album_name']" placeholder="请输入专辑名称"></el-input>
            </div>
            <div class="detail-property-block">
                <span>价格</span>
                <el-input v-model="currentAlbum['price']" placeholder="请输入价格"></el-input>
            </div>
            <div class="detail-property-block">
                <span>发行时间</span>
                <el-date-picker
                    v-model="currentAlbum['public_time']"
                    type="date"
                    placeholder="选择发行日期"
                    format="yyyy-MM-dd"
                    value-format="yyyy-MM-dd">
                </el-date-picker>
            </div>
        </div>

        <div slot="footer" class="dialog-footer">
            <el-button @click="hideDialog">取 消</el-button>
            <el-button type="primary" @click="confirmDialog">确 定</el-button>
        </div>
    </el-dialog>
</template>

<script>
export default {
    name: 'Index',
    props: ['type', 'album', 'baseUrl'],
    data () {
        return {
            currentAlbum: null
        }
    },
    beforeMount() {
        if (this.type === 'add') {
            this.currentAlbum = {
                cover: '',
                album_id: '',
                album_name: '',
                price: '',
                public_time: ''
            }
        } else {
            this.currentAlbum = this.album;
        }
    },
    computed: {
        title() {
            return this.type === 'add' ? '添加专辑' : '修改专辑'
        }
    },
    methods: {
        hideDialog() {
            this.$emit('hideDialog', 'album');
        },
        confirmDialog() {
            if (this.type === 'add' && !this.currentAlbum.cover) {
                this.$message({
                    showClose: true,
                    message: '请上传文件',
                    type: 'error'
                });
                return;
            }
            this.$emit('confirmAlbumDialog', this.currentAlbum);
        },
        upload() {
            let uploadInput = this.$refs.fileInput;
            const formData = new FormData()
            formData.append('cover', uploadInput.files[0])
            let url = `${this.baseUrl}/albums/uploads`;
            fetch(url,{
                method: 'POST',
                body: formData, // "{"name":"Billy","age":18}"
            }).then(response => response.text()).then((response) => {
                console.log(response);
                if (response) {
                    this.currentAlbum.cover = response.toString();
                    this.$message({
                        showClose: true,
                        message: '上传成功',
                        type: 'success'
                    });
                }
            })
        }
    }
}
</script>
<style lang="scss" scope>
    .detail-block {
        padding: 10px 20px;
        .detail-property-block {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;

            &.upload-block {
                & > input {
                    border: 1px solid #DCDFE6;
                    flex: 1;
                    margin-right: 10px;
                }
            }
        }

        .detail-property-block > span {
            width: 100px;
            font-size: 14px;
        }
        .detail-property-block .el-input {
            flex: 1;
        }
    }
    

</style>

