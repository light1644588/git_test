<template>
    <div class="map" :key="mapNode">
        <input 
            v-model="addressKeyword" 
            :placeholder="palceholderName" 
            class="query-input"
            ref="searchRef" />
        <baidu-map 
            class="bmView" 
            :scroll-wheel-zoom="true" 
            :center="location" 
            :zoom="zoom" 
            @ready="handler">
            <div v-show="showLocal">
                <bm-local-search
                    @searchcomplete="searchcomplete"
                    @infohtmlset="infohtmlset"
                    class="query-box" 
                    :pageCapacity=8
                    :keyword="addressKeyword" 
                    :auto-viewport="true">
                </bm-local-search>
            </div>
            <bm-view style="width: 100%; height:500px; flex: 1"></bm-view>
            <bm-marker 
                :position="location" 
                @click="infoWindowOpen"
                v-if="showMarker">
                <bm-info-window 
                    :show="showInfo" 
                    @close="infoWindowClose" 
                    @open="infoWindowOpen">{{palceholderName}}</bm-info-window>
            </bm-marker>
            <bm-geolocation anchor="BMAP_ANCHOR_BOTTOM_RIGHT" :showAddressBar="true" :autoLocation="true"></bm-geolocation>
        </baidu-map>
        <div class="btn-group custom-btns-box" style="margin-top: 20px">
          <Button type="primary" @click="handleEditMap">保存</Button>
          <Button type="default" @click="handleCancelEditMap" style="margin-left:20px;">取消</Button>
        </div>
    </div>
</template>

<script>
export default {
  name: 'ownMap',
  props: {
      curaddressKeyword: {
          type: String,
          default: ''
      },
      curlocation: {
          type: Object,
          default: () => {
              return {
                lng: 116.307852,
                lat: 40.057031
            }
          }
      },
      zoom: {
          type: Number,
          default: 15
      }
  },
  data() {
        return {
            mapNode: 1,
            location:{
                lng: 0,
                lat: 0
            },
            addressKeyword: '',
            palceholderName: '',
            curGelocation: {
                lng: 0,
                lat: 0,
                address: ''
            },
            showLocal: true,
            showInfo: false,
            showMarker: true
        };
    },
    methods: {
        handler ({BMap, map}) {
        
            // center
            this.location.lng = this.curlocation.lng
            this.location.lat = this.curlocation.lat
            this.palceholderName = this.curaddressKeyword
            // emit
            this.curGelocation = {
                lng:  this.curlocation.lng,
                lat:  this.curlocation.latt,
                address:  this.curaddressKeyword
            }
            if(!this.curaddressKeyword) {
                this.showMarker = false
            } else {
                this.showMarker = true
            }
        },
        infoWindowClose() {
            this.showInfo = false
        },
        infoWindowOpen() {
            this.showInfo = true
        },
        // 搜索
        searchcomplete() {
            this.showLocal = true
        },
        infohtmlset(data) {
            this.showLocal = false
            this.location = data.point
            this.curGelocation = {
                lng:  data.point.lng,
                lat:  data.point.lat,
                address:  data.address
            }
        },
        handleEditMap() {
            this.$emit('handleEmitEditMap', this.curGelocation, false)
        },
        handleCancelEditMap() {
            this.$emit('handleCancelEditMap', false)
        }
    }
}
</script>

<style lang="less">
.map {
    position: relative;
    width: 100%;
    .query-input {
        z-index: 999;
        position: absolute;
        width: auto;
        min-width: 22rem;
        padding: 2px;
        margin: 10px;
        line-height: 30px;
        background-color: #fff;
        border: none;
        outline: none;
        // border-radius: .25rem;
        font-size: 14px;
        color: #666;
        // box-shadow: 0 2px 6px 0 rgba(27, 142, 236, 0.5);
    }
    .query-box {
        // display: none;
        position: absolute;
        left: 10px;
        top:43px;
        width: 352px;
        z-index: 100;
        &>div {
            border: none!important;
        }
    }
}
</style>