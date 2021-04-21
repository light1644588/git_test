<template>
    <div>
        <Tree
            :load-data="loadData" 
            :data="dataTree" 
            :render="renderContent" 
            class="demo-tree-render"
            @on-toggle-expand="hanldeToggleExpandNode">
        </Tree>
        <Modal v-model="deleteModal2" title="删除" width="300" @on-ok="handleEmitOk">
            <p v-show='true'>
                确定要删除该部门吗？
            </p>
        </Modal>
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
            addIcon: {
                type: Boolean,
                default: false
            },
            updateIcon: {
                type: Boolean,
                default: false
            },
            deleteIcon: {
                type: Boolean,
                default: false
            },
        },
        data () {
            return {
                currentData: '',
                deleteModal2: false,
                dataTree: this.treeNodes,
                addIcon2: this.addIcon,
                updateIcon2: this.updateIcon,
                deleteIcon2: this.deleteIcon,
                buttonProps: {
                    type: 'default',
                    size: 'small',
                },
                currentId: ''
            }
        },
        methods: {
            renderContent (h, { root, node, data }) {
                let _this = this
                return h('span', {
                    style: {
                        // display: 'inline-block',
                        // width: '100%'
                    },
                    class: {
                        'active-item': this.currentId == data.id ? true : false
                    }
                }, [
                    h('span', {
                        style: {
                            lineHeight: '24px',
                            height: '24px'
                        }
                    }, [
                         h('Icon', {
                            props: {
                                type: data.isBuilding  ? 'md-home' : 'md-folder'
                            },
                            style: {
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
                    h('span', {
                        style: {
                            marginLeft: '5px'
                        },
                        class: {
                            'btn-item': true
                        }
                    }, [
                       h('Button', {
                            props: Object.assign({}, this.buttonProps, {
                                icon: 'md-create'
                            }),
                            style: {
                                marginRight: '4px',
                                height: '24px',
                            },
                            class: {
                                dNone: !_this.updateIcon2,
                            },
                            on: {
                                click: () => { this.edit(data) }
                            }
                        }),
                        h('Button', {
                            props: Object.assign({}, this.buttonProps, {
                                icon: 'md-add'
                            }),
                            style: {
                                marginRight: '4px',
                                height: '24px',
                            },
                            class: {
                                dNone: !_this.addIcon2
                            },
                            on: {
                                click: () => { this.append(data) }
                            }
                        }),
                        h('Button', {
                            props: Object.assign({}, this.buttonProps, {
                                icon: 'md-trash'
                            }),
                            style: {
                                height: '24px',
                            },
                            class: {
                                dNone: !_this.deleteIcon2
                            },
                            on: {
                                click: () => { this.remove(root, node, data) }
                            }
                        })
                    ])
                ]);
            },
            hanldeToggleExpandNode(data) {
                this.$emit('handleToggleExpand', data)
            },
            clickNode(root, node, data) {
                this.currentId = data.id
                this.$emit('handleEmitTreeNode', data, root, node)
            },
            edit (data) {
                this.$emit('handleEmitRename', data)
            },
            append (data) {
                this.$emit('handleAddTreeNode', data)
                // const children = data.children || [];
                // children.push({
                //     title: 'appended node',
                //     expand: true
                // });
                // this.$set(data, 'children', children);
            },
            remove (root, node, data) {
                this.deleteModal2 = true
                this.currentData = {
                    root,
                    node,
                    data
                }
                // console.log(root, node, data);
                // const parentKey = root.find(el => el === node).parent;
                // const parent = root.find(el => el.nodeKey === parentKey).node;
                // const index = parent.children.indexOf(data);
                // parent.children.splice(index, 1);
            },
            handleEmitOk() {
                this.$emit('handleEmitOk', this.currentData)
            },
            loadData (item, callback) {
                
                this.$emit('handleRefreshTreeNode', {
                    item,
                    callback
                })
                // setTimeout(() => {
                //     const data = [
                //         {
                //             title: 'children1',
                //             loading: false,
                //             children: []
                //         },
                //         {
                //             title: 'children2',
                //             loading: false,
                //             children: []
                //         }
                //     ];
                //     callback(data);
                // }, 1000);
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
            vertical-align: text-bottom;
        }
        .ivu-tree-empty {
            padding-top: 10px;
        }
    }
</style>
