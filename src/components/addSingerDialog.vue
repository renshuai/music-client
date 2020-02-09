<template>
    <el-dialog :title="title" :visible="true" :before-close="hideDialog">
        <div v-if="currentSinger" class="detail-block">
            <div class="detail-property-block">
                <span>歌手ID</span>
                <el-input v-model="currentSinger['singer_id']" placeholder="请输入歌手ID"></el-input>
            </div>
            <div class="detail-property-block">
                <span>歌手名称</span>
                <el-input v-model="currentSinger['singer_name']" placeholder="请输入歌手名称"></el-input>
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
    props: ['type', 'singer'],
    data () {
        return {
            currentSinger: null
        }
    },
    beforeMount() {
        if (this.type === 'add') {
            this.currentSinger = {
                singer_id: '',
                singer_name: ''
            }
        } else {
            this.currentSinger = this.singer;
        }
    },
    computed: {
        title() {
            return this.type === 'add' ? '添加专辑' : '修改专辑'
        }
    },
    methods: {
        hideDialog() {
            this.$emit('hideDialog', 'singer');
        },
        confirmDialog() {
            this.$emit('confirmSingerDialog', this.currentSinger);
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

