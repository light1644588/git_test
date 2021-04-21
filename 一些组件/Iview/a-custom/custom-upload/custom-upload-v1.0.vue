<template>
    <div class="upload-com">
        <div v-show="uploadList.length">
            <div class="demo-upload-list" v-for="(item,index) in uploadList" :key="index">
                <template v-if="item.status === 'finished'">
                    <img :src="item.url">
                    <div class="demo-upload-list-cover">
                        <Icon type="ios-eye-outline" @click.native="handleView()"></Icon>
                        <Icon type="ios-trash-outline" @click.native="handleRemove(item)"></Icon>
                    </div>
                </template>
                <template v-else>
                    <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
                </template>
            </div>
        </div>
        
        <div v-show="!uploadList.length">
            <Upload
                name="myFile"
                ref="upload"
                :on-success="handleSuccess"
                :format="['jpg','jpeg','png']"
                :on-format-error="handleFormatError"
                :before-upload="handleBeforeUpload"
                :headers="Header"
                :action="Action"
                style="display: inline-block;width:58px;">
                <div class="upload-box" style="width: 58px;height:58px;line-height: 58px;">
                    <Icon type="ios-add" size="58" class="upload-icon" />
                </div>
            </Upload>
        </div>
       <Modal title="预览图片"  v-model="visible"  class="upload-box-com">
            <img :src="priviewImg" v-if="visible"   style="max-width: 80%; display: block; margin: 20px auto;">
            <div slot="footer" style="display:none;"></div>
        </Modal>
        <!-- <Modal   style="padding: 40px;">
            <img style="width: 50%; margin:0 auto;display: blcok;">
        </Modal> -->
    </div>
</template>
<script>
import { getToken } from '@/libs/util.js'
import { UPLOADIMG, getImg } from '@/api/common.js'
    export default {
        name: 'upload-com',
        props: {
            multiple: false,
            uploadLists: {
                type: Array,
                default: function () {
                    return []
                }
            }
        },
        data () {
            return {
                Action: UPLOADIMG,
                Header: {
                    'Authorization': getToken()
                },
                uploadList: this.uploadLists,
                priviewImg: '',
                visible: false,
                
            }
        },
        methods: {
             axios_getImg(url) {
                return new Promise((reslove, reject) => {
                    getImg(getToken(), url).then(res => {
                        let streamObj = res.data
                        let blob = new Blob([streamObj], {type: 'image/png'})
                        let reader  = new FileReader();
                        let imgHref = ''
                        reader.addEventListener("load", function () {
                            imgHref = reader.result;
                            reslove(imgHref)
                        }, false);
                        if (blob) {
                            reader.readAsDataURL(blob);
                        }
                    }).catch(err => {
                        reslove('')
                    })

                })
            },
            handleView (name) {
                this.visible = true;
                this.priviewImg = this.uploadList[0].url
                // console.log(this.uploadList, 111);
            },
            handleRemove (file) {
                // const fileList = this.$refs.upload.fileList;
                // this.$refs.upload.fileList.splice(fileList.indexOf(file), 1);
                this.uploadList = []
                this.$refs.upload.fileList = []
                this.$emit('handleUploadRemove', '')
            },
            async handleSuccess (res, file) {
                
                if(res.resultCode !== 0) {
                    this.$Message.error({
                        content: res.resultMsg
                    })
                } else {
                    let filePath = res.resultData.filePath
                    this.$emit('handleUploadSuccess', filePath)
                    file.filePath = filePath
                    file.url = await this.axios_getImg(filePath)
                    this.uploadList = []
                    this.uploadList.push(file)
                }  
            },
            handleFormatError (file) {
               this.$Message.error('请选择正确的图片格式');
            },
            handleMaxSize (file) {
                // :max-size="2048"
                // :on-exceeded-size="handleMaxSize"
               this.$Message.error('请保持图片大小不超过2M');
            },
            handleBeforeUpload () {
                // const check = this.uploadList.length < 5;
                // if (!check) {
                //     this.$Notice.warning({
                //         title: 'Up to five pictures can be uploaded.'
                //     });
                // }
                // return check;
            }
        },
        mounted () {
            // this.uploadList = this.$refs.upload.fileList;
            // console.log(this.uploadList, 111);
        }
    }
</script>
<style lang="less">
    .upload-com {
        .ivu-upload-select {
            border: 1px solid #dcdee2;
            border-radius: 0px;
        }
        .ivu-upload {
            .ivu-upload-list {
                display: none!important;
            }
        }
    }

    .demo-upload-list{
        display: inline-block;
        width: 60px;
        max-width: 100px;
        // height: 60px;
        text-align: center;
        line-height: 60px;
        // border: 1px solid transparent;
        border: none;
        border-radius: 0px;
        overflow: hidden;
        background: #fff;
        position: relative;
        box-shadow: 0 1px 1px rgba(0,0,0,.2);
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
</style>
