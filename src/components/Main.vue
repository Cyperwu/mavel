<template>
  <div>
    <div id="map">
    </div>
    <div id="utils">
      <div id="docker_bottom">
        <div class="docker_button">
          <Button @click="changeMapType" id="changeBtn" class="docker_button">Change</Button>
        </div>
        <div class="docker_button">
          <CheckboxGroup v-model="visibleElements">
          <Checkbox label="bg">地图背景</Checkbox>
          <Checkbox label="point">兴趣点</Checkbox>
          <Checkbox label="road">道路</Checkbox>
          <Checkbox label="building">建筑物</Checkbox>
          </CheckboxGroup>
          <Button @click="setVisible">设置可视</Button>
        </div>
        <div class="docker_button">
          <Button @click="activeAddPoint">打点</Button>
          <!-- <Button @click="activeDeletePoint">删点</Button> -->
        </div>
        <div class="docker_button">
          3j
        </div>
        <div class="docker_button">
          <Button @click="setMapZ">setMap()</Button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'

var AMap = window.AMap
var AMapUI = window.AMapUI
AMapUI.setDomLibrary($)
export default {
  name: 'main',
  data () {
    return {
      mapType: [
        'amap://styles/normal',
        'amap://styles/8e3decd113cb80507fffdc0737c84ee8'
      ],
      currMap: 0,
      visibleElements: [],
      addPointStatus: 0,
      addPointListener: {},
      deletePointStatus: 0,
      infoWindow: {},
      currentMarker: {},
      infoDescWindow: {}
    }
  },
  components: {
  },
  watch: {
    addPointStatus: function (n, o) {
      var _this = this
      if (n === 1) {
        _this.addPointListener = AMap.event.addListener(_this.map, 'click', function (e) {
        // _this.map.on('click', function (e) {
          var longtitude = e.lnglat.getLng()
          var latitude = e.lnglat.getLat()
          var marker = new AMap.Marker({
            position: [longtitude, latitude]
          })
          _this.currentMarker = marker
          marker.on('click', function (e) {
            var marker = e.target
            if (_this.addPointStatus === 1) {
              marker.setMap(null)
            } else {
              if (!marker.desc) {
                var isOpen = _this.infoDescWindow.getIsOpen()
                if (isOpen) _this.infoDescWindow.close()
              } else {
                _this.infoDescWindow.setContent(marker.desc)
                _this.infoDescWindow.open(_this.map, marker.getPosition())
              }
            }
          })
          marker.setMap(_this.map)
          _this.infoWindow.open(_this.map, [longtitude, latitude])
          AMap.event.addListenerOnce(_this.map, 'click', function (e) {
            $('#info_name').val('')
            $('#info_desc').val('')
          })
        })
      } if (n === 0) {
        AMap.event.removeListener(_this.addPointListener)
        var isOpen = _this.infoWindow.getIsOpen()
        $('#info_name').val('')
        $('#info_desc').val('')
        if (isOpen) _this.infoWindow.close()
      }
    },
    deletePointStatus: function (n, o) {
      if (n === 1) {
      }
    }

  },
  methods: {
    changeMapType () {
      this.currMap = 1 - this.currMap
      this.map.setMapStyle(this.mapType[this.currMap])
    },
    setVisible () {
      this.map.setFeatures(this.visibleElements)
    },
    activeAddPoint () {
      this.addPointStatus = 1 - this.addPointStatus
    },
    setMapZ () {
    },
    setMarkerComment (name = '', desc = '') {
      this.currentMarker.name = name
      this.currentMarker.desc = desc
      this.currentMarker.setLabel({
        offset: new AMap.Pixel(20, 20),
        content: name
      })
    }
  },
  mounted () {
    var _this = this
    var map = new AMap.Map('map', {
      resizeEnable: true,
      zoom: 11,
      center: [116.397428, 39.90923]
    })

    var vue = Object.getPrototypeOf(_this)
    vue.map = map

    AMap.plugin(['AMap.ToolBar', 'AMap.Scale', 'AMap.OverView'],
      function () {
        map.addControl(new AMap.ToolBar())

        map.addControl(new AMap.Scale())

        map.addControl(new AMap.OverView({isOpen: true}))
      })

    _this.infoDescWindow = new AMap.InfoWindow({offset: new AMap.Pixel(0, -30)})
    _this.infoDescWindow.on('close', function () {

    })

    var content = `
    <div>
      <div style="background:#fff; border:1px solid #8c8c8c; padding:4px">
        <input type="text" style="float:left; margin-bottom:4px" id="info_name" placeholder=请输入名称 />
        <br/>
        <textarea cols="40" rows="5" id="info_desc" placeholder=请输入备注></textarea>
        <br/>
        <button id="info_submit">确定</button> <button id="info_cancel">取消</button>
      </div>
      <div class="info-bottom" style="position:relative;top:0;margin:0px auto">
        <img src="http://webapi.amap.com/images/sharp.png" style="top:-1px;position:relative">
      </div>
    </div>
    `

    AMapUI.defineTpl('ui/overlay/SimpleInfoWindow/tpl/container.html', [], function () {
      return content
    })

    AMapUI.loadUI(['overlay/SimpleInfoWindow'], function (SimpleInfoWindow) {
      _this.infoWindow = new SimpleInfoWindow({
        offset: new AMap.Pixel(15, -15)
      })
      _this.infoWindow.get$Container().on('click', '#info_cancel', function (e) {
        _this.infoWindow.close()
      })
      _this.infoWindow.get$Container().on('click', '#info_submit', function (e) {
        var infoName = $('#info_name').val()
        var infoDesc = $('#info_desc').val()
        _this.setMarkerComment(infoName, infoDesc)
        _this.infoWindow.close()
      })
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  margin: auto;
  width: 100%;
  height: 100%;
  float: left;
}
#changeBtn {
}
#docker_bottom {
  position: absolute;
  height: 94px;
  width: 40%;
  background: #f0f;
  bottom: 0;
  left: 50%;
  transform: translate(-50%, 0);
  display: flex;
  display: -webkit-flex;
}
.docker_button {
  flex: 1;
  -webkit-flex: 1;
}
</style>
