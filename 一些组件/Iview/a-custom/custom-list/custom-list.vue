<template>
  <div class="sec-list">
    
    <!-- input区域 -->
    <div class="query-area">
        <Input
            class="top-input"
            search
            v-model="productName"
            @on-blur="handleSearchPro"
            @on-search="handleSearchPro"
            :placeholder="productPlace"
        />
    </div>

    <h3 class="p-sm query-title" v-if="titleName.length">{{titleName}}</h3>

    <List border class="query-list" v-if="showProduct">
      <ListItem
        v-for="(item, index) in productList"
        :class="[
          { 'aside-list-item': true },
          { active: showActive && currentIndex === index }
        ]"
        :key="index"
      >
        <template v-if="!showInput || currentIndex !== index">
            <span :title="item.title || item.productName || '' "  @click="handleActive(index)">{{ item.title || item.productName || index }}</span>
            <div 
              style="display:none ;width: 30px;height:42px;line-height: 42px;text-align: center;" 
              class="icon-area" 
              @click="handleActiveInput(index, item.title || item.productName || '')"
              v-if="showIcon">
                <Icon type="md-create" size="15" style="color: #0077FF;"></Icon>
            </div>
        </template>
        <template v-else-if="showInput && currentIndex === index">
             <Input v-model="currentName" placeholder="请输入" style="width: 172px;height:32px;line-height:32px;" />
             <div style="width: 30px;height:42px;line-height: 42px;text-align: center;" @click="handleTrueInput(index)">
                <Icon type="ios-checkmark" size="30" style="color: #02BB00;"></Icon>
            </div>
            <div style="width: 30px;height:42px;line-height: 42px;text-align: center;" @click="handleActive(index)">
                <Icon type="ios-close" size="30" style="color: #FF5240;" ></Icon>
            </div>
        </template>
        <template v-else>
            <span @click="handleActive(index)">{{ item.title || item.productName || '' }}</span>
            <div style="width: 30px;height:42px;line-height: 42px;text-align: center;">
                <Icon type="md-bulb" size="15" style="color: #FF5240;"></Icon>
            </div>
        </template>
        
      </ListItem>
    </List>
    <div v-else class="left-aside-nolist" style="text-align: center;" >暂无数据</div>
  </div>
</template>

<script>
export default {
  name: "sec-list",
  data() {
      return {
          // key: 0,
          currentIndex: '',
          currentName: '',
          productName: '',
          showActive: false,
          showInput: false,
          showProduct: this.productList.length ? true : false,
          productPlace: '请输入关键词搜索'
      }
  },
  props: {
    showIcon: {
      default: true,
      type: Boolean
    },
    titleName: {
        type: String
    },
    productList: {
        type: Array,
        default: () => {
          return [
           
          ]
        }
    }
  },
  watch: {
      productList: {
        handler(val) {
            if([...val].length) {
              this.showProduct = true
            } else {
              this.showProduct = false
            }
        },
        deep: true
      }
  },
  // mounted() {
  //   this.productList.length ? this.showProduct = true :  this.showProduct = false
  //   console.log(this.showProduct);
  // },
  methods: {
      handleSearchPro() {
      
         this.$emit('handleQueryList', this.productName);
      },
    //   点击关闭编辑/取消编辑
      handleActive(index) {
          this.currentIndex = index
          this.showActive = true
          this.showInput = false
          this.$emit('handleChangeActive',index);
      },
    //   点击编辑激活
      handleActiveInput(index, value) {
          this.currentName = value
        //   console.log('输入')
          this.currentIndex = index
          this.showActive = false
          this.showInput = true
          // this.$emit('handleActiveInputEmit', index);
      },
    //   点击编辑确定
     handleTrueInput(index) {
          // this.productList[index].name = this.currentName
          this.currentIndex = index
          this.showActive = true
          this.showInput = false
          this.$emit('handleEditItem',index, this.currentName);
     }
  }
};
</script>

<style lang="less">
.sec-list {
  display: block;
  // width: 240px;
  width: 222px;
  height: 100%;
  background: #ffffff;
  .query-area {
      border-bottom: 1px solid #E3E8EE;
      .ivu-input-search-icon {
          right: 20px;
      }
      .top-input {
         margin: 10px -1px;
         input {
            width: 222px;
            height: 32px;
            font-size: 13px;
            line-height: 32px;
            background: #F5F7F9;
            // border-radius: 16px;
            // border: 1px solid #D7DDE4;
            outline: none;
         }
      }
  }
  .query-title {
    padding-left: 10px;
    font-size: 15px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: bold;
    color: #657180;
    line-height: 31px;
  }
  .query-list {
      width: 100%;
      padding-bottom: 10px;
      border: none;
      .aside-list-item {
       
        &:hover {
            background-color: aliceblue;
            .icon-area {
                 display: inline-block!important;
            }
        }
        &.active {
            background-color: #2d8cf0;
            color: #fff;
            i {
                color: #fff!important;
            }
        }
      }
     
      li {
          padding: 0;
          padding-left: 10px;
          padding-right: 10px;
          height: 42px;
          color: #4A4A4A;
          font-size: 13px;
          line-height: 42px;  
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          cursor: pointer;
          span {
              display: block;
              width: 100%;
              white-space: nowrap;
              text-overflow: ellipsis;
              overflow: hidden;
          }
          &:last-child {
              border-bottom: 1px solid #e8eaec;
          }
      }
  }
}
</style>
