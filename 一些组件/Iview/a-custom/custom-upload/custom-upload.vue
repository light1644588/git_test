<template>
    <div class="">
        <div class="demo-upload-list" v-show="!showUpload">
            <template v-if="!showUpload">
                <img :src="uploadImg">
                <div class="demo-upload-list-cover">
                    <Icon type="ios-eye-outline" @click.native="handleView"></Icon>
                    <Icon type="ios-trash-outline" @click.native="handleRemove"></Icon>
                </div>
            </template>
        </div>
        <Upload
            v-if = "showUpload"
            ref="upload"
            action="//jsonplaceholder.typicode.com/posts/"
            :before-upload="handleBeforeUpload"
            style="display: inline-block;">
            <div style="width: 58px;height:58px;line-height: 58px;">
                <Icon type="ios-add" style="border: 1px solid #D7DDE4;" size="58"></Icon>
            </div>
        </Upload>
        <!-- 图片预览 -->
        <Modal  v-model="visible"  class="upload-box-com">
            <img :src="uploadImg" v-if="visible" style="max-width: 80%; display: block; margin: 20px auto;">
            <div slot="footer" style="display:none;">
            </div>
        </Modal>
        <!-- 删除确认 -->
         <Modal
            title="确认框"
            v-model="remodel"
            @on-ok="handleok"
            @on-cancel="handlecancel">
            <p>请确认是否删除？</p>
        </Modal>
    </div>
</template>
<script>
    import { fileToBase64, addBaseHeader, removeBaseHeader } from '@/libs/common'
    export default {
        name: 'custom-upload',
        data () {
            return {
                visible: false,
                showUpload: true,
                uploadImg: '',
                remodel: false
            }
        },
        props: {
            uploadFile: String
        },
        watch: {
            uploadFile: function(val) {
                if(!val) {
                    this.uploadImg = ''
                    this.showUpload = true
                } else {
                    this.uploadImg = addBaseHeader(val)
                    this.showUpload = false
                }
            }
        },
        methods: {
            handleView () {
                this.visible = true;
            },
            handleok() {
                this.remodel = false
                this.uploadImg = ''
                this.showUpload = true
                this.$emit('iUpload', this.uploadImg);
            },
            handlecancel() {
                this.remodel = false
            },
            handleRemove () { 
                this.remodel = true
                // this.uploadImg = ''
                // this.showUpload = true
                // this.$emit('iUpload', this.uploadImg);
            },
            handleSuccess (res, file) {
                // :on-success="handleSuccess"
                // :on-error="handleError"
                // action="//jsonplaceholder.typicode.com/posts/"
                this.uploadImg = 'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2523798506,1659891722&fm=11&gp=0.jpg'
                this.showUpload = false
                this.$emit('iUpload', this.uploadImg);
            },
            handleError (err, file) {
               
                this.uploadImg = 'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2523798506,1659891722&fm=11&gp=0.jpg'
                this.showUpload = false
                this.$emit('iUpload', this.uploadImg);
            },
            handleFormatError (file) {
                
            },
            handleMaxSize (file) {
               
            },
            handleBeforeUpload (file) {
               fileToBase64(file).then(res => {
                    // console.log(res);
                    this.uploadImg = res
                    this.showUpload = false
                    this.$emit('iUpload', removeBaseHeader(res))
               })
            }
        }
    }
</script>
<style lang="less">
    .demo-upload-list{
        display: inline-block;
        width: 60px;
        // min-height: 60px;
        text-align: center;
        // line-height: 60px;
        /* border: 1px solid transparent; */
        border-radius: 4px;
        overflow: hidden;
        background: #fff;
        position: relative;
        /* box-shadow: 0 1px 1px rgba(0,0,0,.2); */
        margin-right: 4px;
    }
    .demo-upload-list img{
        width: 100%;
        height: 100%;
    }
    .demo-upload-list-cover{
        display: none;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        background: rgba(0,0,0,.6);
    }
    .demo-upload-list:hover .demo-upload-list-cover{
        display: block;
    }
    .demo-upload-list-cover i{
        color: #fff;
        font-size: 20px;
        cursor: pointer;
        margin: 0 2px;
    }
    .upload-box-com {
        .ivu-modal-footer {
            display: none;
        }
        .ivu-modal {
            max-width: 60%;
        }
    }
</style>
