<template>
	<div class="bg-white">
    <div class="tree-box">
        <div class="zTreeDemoBackground left role-box">
            <ul 
               :id="domId" class="ztree p-sm" 
               :style="{height:ztreeHeight || 'auto'}"
               @onCheck="handleCheck"
            ></ul>
        </div>
    </div>
	</div>
</template>

<script>
export default {
  components: {
  },
  props: {
    reserveHeight: {
      type: [String, Number, Boolean],
      default: '123px'
    },
    editable: {
      type: Boolean,
      default: true
    },
    domId: {
      type: String,
      default: 'treeDemo'
    },
    isCheck: {
      type: Boolean,
      default: false
    },
    zNodes: {
      default: function() {
        return [
         { 
          name: '物业运营管理',
          checked: false,
          open: true,
          children: [
            { 
              name: '企业信息管理',
              checked: true,
              children: [
                { name: '查询',checked: false },
                { name: '新建',checked: false },
                { name: '编辑',checked: false },
                { name: '删除',checked: false },
                { name: '导入',checked: false },
                { name: '导出',checked: false }
              ] },
            { 
              name: '访客信息管理',
              checked: false,
              children: [
                { name: '查询',checked: false },
                { name: '新建',checked: false },
                { name: '编辑',checked: false },
                { name: '删除',checked: false },
                { name: '导入',checked: false },
                { name: '导出',checked: false }
              ] },
             { 
              name: '产品配置管理',
              checked: false,
              children: [
                { name: '查询',checked: false },
                { name: '新建',checked: false },
                { name: '编辑',checked: false },
                { name: '删除',checked: false },
                { name: '导入',checked: false },
                { name: '导出',checked: false }
              ] },
              { 
              name: '统计分析',
              checked: false,
              children: [
                { name: '查询' ,checked: false},
                { name: '新建' ,checked: false},
                { name: '编辑' ,checked: false},
                { name: '删除' ,checked: false},
                { name: '导入' ,checked: false},
                { name: '导出' ,checked: false}
              ] },
            { name: '没有子节点',checked: false, isParent: true }
          ] },
         { 
          name: '企业后台管理',
          checked: false,
          open: true,
          children: [
            { 
              name: '单位人员管理',
              checked: false,
              children: [
                { name: '查询' ,checked: false},
                { name: '新建' ,checked: false},
                { name: '编辑' ,checked: false},
                { name: '删除' ,checked: false},
                { name: '导入' ,checked: false},
                { name: '导出' ,checked: false}
              ] },
            { 
              name: '预约审核',
              checked: false,
              children: [
                { name: '查询' ,checked: false},
                { name: '新建' ,checked: false},
                { name: '编辑' ,checked: false},
                { name: '删除' ,checked: false},
                { name: '导入' ,checked: false},
                { name: '导出' ,checked: false}
              ] }
          ] },
         { 
            name: '访客端',
            checked: false,
            open: true,
            children: [
              { 
                name: '新增预约',
                checked: false,
                children: [
                  { name: '查询' ,checked: false},
                  { name: '新建' ,checked: false},
                  { name: '编辑' ,checked: false},
                  { name: '删除' ,checked: false},
                  { name: '导入' ,checked: false},
                  { name: '导出' ,checked: false}
                ] },
              { 
                name: '我的预约',
                checked: false,
                children: [
                  { name: '查询' ,checked: false},
                  { name: '新建' ,checked: false},
                  { name: '编辑' ,checked: false},
                  { name: '删除' ,checked: false},
                  { name: '导入' ,checked: false},
                  { name: '导出' ,checked: false}
                ] }
            ] },
         { name: '没有子节点',checked: false, isParent: true }
      ]
      }
    }
  },
  computed: {
    ztreeHeight () {
      if (typeof this.reserveHeight === 'number') {
        return this.reserveHeight + 'px'
      } else {
        return 'calc(100vh - ' + this.reserveHeight + ')'
      }
    }
  },
  data: function () {
    return {
      currentZNodes: '',
      deleteModal2: false,
      maskZtree: false, // 目录树遮罩显示
      newCount: 1,
      setting: {
        edit: {
          enable: this.editable,
          showRemoveBtn: true,
          removeTitle: '删除',
          showRenameBtn: true,
          renameTitle: '编辑'
        },
        data: {
          simpleData: {
            enable: true
          }
        },
        callback: {
          beforeDrag: this.beforeDrag,
          beforeDrop: this.beforeDrop,
          beforeRemove: this.zTreeBeforeRemove,
          onClick: this.zTreeOnClick,
          onCheck: this.onCheck
        },
        view: {
          showLine: false, // 是否显示线条
          showIcon: true, // 是否显示节点前面的图标，默认为true
          showTitle: true,
          addHoverDom: this.editable ? this.addHoverDom : null,
          removeHoverDom: this.removeHoverDom
        },
        check: {
          enable: this.isCheck,
          chStyle: 'checkbox'
        }

      },
    }
  },
  methods: {
    freshArea: function () {
      $.fn.zTree.init($('#' + this.domId), this.setting, this.zNodes)
    },
    beforeDrag (treeId, treeNodes) {
        	for (var i = 0, l = treeNodes.length; i < l; i++) {
        		if (treeNodes[i].drag === false) {
        			return false
        		}
        	}
        	return true
    },
    zTreeBeforeRemove (treeId, treeNode) {
      this.deleteModal2 = true
      this.deleteTreeNode = treeNode
      return false
    },
    beforeDrop (treeId, treeNodes, targetNode, moveType) {
        	return targetNode ? targetNode.drop !== false : true
    },
    removeHoverDom (treeId, treeNode) {
      $('#addBtn_' + treeNode.tId).unbind().remove()
      $('#details_' + treeNode.tId).unbind().remove()
      $('#copy_' + treeNode.tId).unbind().remove()
    },
    addHoverDom (treeId, treeNode) {
        	var sObj = $('#' + treeNode.tId + '_span')
        	// 新增
        	if (treeNode.editNameFlag || $('#addBtn_' + treeNode.tId).length > 0) return
        	var addStr = "<span class='button add' id='addBtn_" + treeNode.tId + "' title='新增' onfocus='this.blur();'></span>"
        	sObj.after(addStr)
        	var btn = $('#addBtn_' + treeNode.tId)
        	if (btn) {
        btn.bind('click', function () {
        		var zTree = $.fn.zTree.getZTreeObj(this.domId)
        		zTree.addNodes(treeNode, {
        			id: (100 + this.newCount),
        			pId: treeNode.id,
        			name: 'new node' + (this.newCount++)
        		})
        		return false
        	})
      }
    },
    zTreeOnClick (event, treeId, treeNode) {
      // console.log(event.target)
      // console.log(treeId)
      // console.log(treeNode.name)
      this.$emit('ztreeName', treeNode.name, this.maskZtree)
    },
    // onCheck(e, treeId, treeNode) {
    //   console.log(treeNode)
    //   console.log(this.currentZNodes)
    // },
    handleCheck(event,treeId,treeNode) {
      // console.log(event,treeId,treeNode);
    }
  },
  mounted () {
    $.fn.zTree.init($('#' + this.domId), this.setting, this.zNodes).expandAll(true)
    this.currentZNodes = this.zNodes
  }
}
</script>
<style lang="less">
    .ztree{
      /* height: calc(100vh - 175px); */
      overflow: auto;
    }
    .box-title .fa{
        float:right;line-height: 20px;
    }
    .bg-white {
      .role-box {
        margin-left: 40px;
        .level0 > ul {
          margin-left: 20px!important;
        }
        .level1 {
            display: flex;
            justify-content: flex-start;
            align-items: center;
            
        }
        .ztree li ul {
          margin: 0;
          padding: 0;
          display: flex;
        }
      }
    }
   
</style>
</style>
