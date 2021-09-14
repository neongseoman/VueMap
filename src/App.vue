<template>
  <div id="app">
    <div id="map_wrap">
      <div id="map">
        <search-destination id="search_wrap" style="float: left;width: 300px"
        v-on:placeDataPass="addMarker"></search-destination>
      </div>
    </div>
  </div>
</template>

<script>
import searchDestination from "@/components/searchDestination.vue";

export default {
  name: 'App',
  data() {
    return {
      map:null,
      keyword: '',
      markers: [],
      infowWindows: []
    }
  },

  mounted() {
    window.kakao && window.kakao.maps ? this.initMap() : this.addKakaoMapScript;
  },
  methods:{
    initMap() {
      const container = document.getElementById('map');
      const options = {
        center: new kakao.maps.LatLng(33.450701, 126.570667),
        level: 3
      };
      this.map = new kakao.maps.Map(container,options);
    },

    addKakaoMapScript(){
      const script = document.createElement('script');
      script.onload = () => kakao.maps.load(this.initMap);
      script.src="//dapi.kakao.com/v2/maps/sdk.js?appkey=e1618b6825cc4783bc5f5e882244b42b&libraries=services"
      document.head.appendChild(script);
    },

    addMarker(data){
      let bounds = new kakao.maps.LatLngBounds();
      for(let i =0 ;i < data.length;i++){
        this.displayMarker(data[i]);
        bounds.extend(new kakao.maps.LatLng(data[i].y,data[i].x));
      }

      this.map.setBounds(bounds)
    },
    displayMarker(place) {
      const position = new kakao.maps.LatLng(place.y, place.x)
      const marker = new kakao.maps.Marker({
        map:this.map,
        position
      });
      this.markers.push(marker)

      kakao.maps.event.addListener(marker, 'click', () => {
        // this.displayInfoWindow(marker,place,position)
        this.infowWindows.push(this.displayInfoWindow(marker,place,position))
        this.map.panTo(position)
      });
    },
    // removeMarkers(){
    //   console.log("removeMarkers")
    //   for(let i = 0; i < this.markers.length;i++){
    //     this.markers[i].setMarkers(null)
    //   }
    //   this.markers = []
    // },
    // markersVaildCheck(){
    //   if (this.markers.length){
    //     this.removeMarkers()
    //   }
    //   else {
    //     return 0
    //   }
    // },

    displayInfoWindow(marker, place, position) {
      let infoWindow = new kakao.maps.InfoWindow({
        position,
        removable:true
      });
      const content = '<div id="infowindow", style="width:180px;height:200px;padding:15px 10px;">' +
          '<span id="placeinfo">'+place.place_name +
          '<p>'+place.phone+'</p>' + '<p>' + place.address_name + '</p>'+'</span>'+
          '<button type="button" onclick="closeInfoWindow()">닫기</button>'+'</div>'
      infoWindow.setContent(content)
      infoWindow.setMap(this.map)
      return infoWindow
    },

    closeInfoWindow(infoWindow){
      infoWindow.setMap(null)
    }
  },

  components:{
    searchDestination
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#map {width:100%;height:700px;z-index: 1}
body{font: 12px/1.5 'Malgun Gothic','돋움',dotum,sans-serif;}
#search_wrap {position:absolute;top:0;left:0;bottom:0;width:250px;margin:10px 0 30px 10px;padding:5px;
  overflow-y:auto;background:rgba(255, 255, 255, 0.7);z-index: 5;font-size:12px;border-radius: 10px;}

</style>
