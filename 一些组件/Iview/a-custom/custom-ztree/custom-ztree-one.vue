<template>
    <div>
        <Tree
            :load-data="loadData" 
            :data="dataTree" 
            :render="renderContent" 
            class="demo-tree-render"
            @on-toggle-expand="hanldeToggleExpandNode">
        </Tree>
       
    </div>
</template>
<script>
    export default {
        name: 'custom-ztree',
        props: {
            treeNodes: {
                type: Array,
                default: () => {
                    return [
                        {
                            title: '根目录',
                            loading: false,
                            children: []
                        }
                    ]
                }
            },
            isSpace: {
                type: Boolean,
                default: true
            }
        },
        data () {
            return {
                currentData: '',
                dataTree: this.treeNodes,
                buttonProps: {
                    type: 'default',
                    size: 'small',
                },
                currentId: '',
                is_space: this.isSpace
            }
        },
        methods: {
            renderContent (h, { root, node, data }) {
                let _this = this
                return h('span', {
                    class: {
                        'active-item': this.currentId == data.id ? true : false
                    }
                }, [
                    h('span', {
                        style: {
                            lineHeight: '24px',
                            height: '24px',
                            display: 'inline-flex',
                            alignItems: 'center',
                        }
                    }, [
                        h('span', {
                            class: {
                                'sprite-img': true,
                                'sprite-img-all':  data.parentId == 0, //全部
                                'sprite-img-plot': data.typeCode == 1, //小区
                                'sprite-img-door': data.typeCode == 2, //大门
                                'sprite-img-building': data.typeCode == 3, //建筑物
                                'sprite-img-car': data.typeCode == 4, //停车场
                                'sprite-img-road': data.typeCode == 5, //道路
                                'sprite-img-cell': data.typeCode == 7, //单元
                                'sprite-img-builds': data.typeCode == 6, //楼栋
                                'sprite-img-floor': data.typeCode == 8, //楼层
                                'sprite-img-room': data.typeCode == 9, //房间
                                'sprite-img-unit': !this.is_space && data.typeCode == 1, //单位
                                'sprite-img-sector': !this.is_space && data.typeCode == 2, //部门
                                'sprite-img-job': !this.is_space && data.typeCode == 3, //岗位
                            },
                            style: {
                                display: 'inline-block',
                                width: '16px',
                                height: '16px',
                                marginRight: '8px'
                            }
                        }),
                        h('span', {
                             style: {
                                cursor: 'pointer'
                             },
                            attrs: {
                                title: data.title
                            },
                             on: {
                                click: () => { this.clickNode(root, node, data) }
                            }
                        }, data.title)
                    ]),
                ]);
            },
            hanldeToggleExpandNode(data) {
                this.$emit('handleToggleExpand', data)
            },
            clickNode(root, node, data) {
                this.currentId = data.id
                this.$emit('handleEmitTreeNode', {
                    data, 
                    root, 
                    node
                })
            },
            edit (data) {
                this.$emit('handleEmitRename', data)
            },
            append (data) {
                this.$emit('handleAddTreeNode', data)
            },
            remove (root, node, data) {
                this.deleteModal2 = true
                this.currentData = {
                    root,
                    node,
                    data
                }
              
            },
            handleEmitOk() {
                this.$emit('handleEmitOk', this.currentData)
            },
            loadData (item, callback) {
                
                this.$emit('handleRefreshTreeNode', {
                    item,
                    callback
                })
               
            }
        }
    }
</script>

<style lang="less">
    .demo-tree-render .ivu-tree-title{
        width: 100%;
    }
    .demo-tree-render {
        margin-left: 10px;
        margin-right: 10px;
        button {
            outline: none;
            border: 0;
        }
        .btn-item {
            display: none;
        }
        .d-none {
            display: none!important;;
        }
        .dNone {
            display: none!important;;
        }
        .active-item {
            display: inline-flex;
            align-items: center;
            // background-color:  #2d8cf0;
            // width: 100%;
            span {
                color:  #2d8cf0;
            }
            button {
                background-color: transparent;
                color: #2d8cf0;
            }
            .btn-item {
                display: inline-block;
                // display: inline-flex;
                // align-items: center;
            }
        }
        .ivu-btn-icon-only.ivu-btn-small {
            padding: 2px;
        }
        .ivu-tree-arrow {
            vertical-align: top;
            padding-top: 3px;
        }
        .ivu-tree-empty {
            padding-top: 10px;
        }
    }
    .sprite-img {
        // 间隔4px 大小16*16
        background-image: url('../../../assets/images/tree-icon.png');
        background-size: 150px 280px;
        background-position: 0px 0px;
    }
    .sprite-img-all { //全部
        background-position: 0px 0px;
    }
    .sprite-img-plot { //小区
        background-position: 0px -44px;
    }
    .sprite-img-door { //大门
        background-position: 0px -174px;
    }
    .sprite-img-building { //建筑物
        background-position: 0px -64px;
    }
    .sprite-img-car { //停车场
        background-position: 0px -218px;
    }
    .sprite-img-road { //道路
        background-position: 0px -196px;
    }
    .sprite-img-cell { //单元
        background-position: 0px -108px;
    }
    .sprite-img-builds { //楼栋
        background-position: 0px -88px;
    }
    .sprite-img-floor { //楼层
        background-position: 0px -132px;
    }
    .sprite-img-room { //房间
        background-position: 0px -152px;
    }
    .sprite-img-unit { //单位
        background-position: -44px -64px;
    }
    .sprite-img-sector { //部门
        background-position: -44px -87px;
    }
    .sprite-img-job { //岗位
        background-position: -24px -176px;
    }
</style>
