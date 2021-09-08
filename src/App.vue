<template>
  <div id="app">
    <div id="map_wrap">
      <div id="map">
        <search id="search_wrap" style="float: left"></search>
      </div>
    </div>
  </div>
</template>

<script>
import search from "@/components/search.vue";

export default {
  name: 'App',
  data() {
    return {
      map:null,
      keyword: ''
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

  },
  components:{
    search
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
