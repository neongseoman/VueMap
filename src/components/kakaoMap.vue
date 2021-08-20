<template>
  <div>
    <div id="map" class="map"></div>
    <input type="text" id="keyword" v-model="keyword">
    <button v-on:click="searching">검색</button>
  </div>
</template>

<script>
export default {
  name: "KakaoMap",
  data() {
    return {
      map : null,
      keyword: '',
      infowindow : null
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

    //keyword로 장소 검색하기
    searching(){
      var ps = new kakao.maps.services.Places();
      ps.keywordSearch(this.keyword,this.placeSearchCB)
    },

    // eslint-disable-next-line no-unused-vars
    placeSearchCB(data, status,pagination){
      if(status === kakao.maps.services.Status.OK){
      //  display place and marker

        var bounds = new kakao.maps.LatLngBounds();
        console.log(bounds)
        for (let i = 0; i< data.length;i++){
          this.displayMarker(data[i]);
          bounds.extend(new kakao.maps.LatLng(data[i].y,data[i].x))
        }

      }else if(status === kakao.maps.services.Status.ZERO_RESULT){
        alert("검색결과가 없습니다.")
      }else if(status === kakao.maps.services.Status.ERROR){
        alert('검색 중 오류가 발생했습니다.')
      }

      this.map.setBounds(bounds);
    },

    displayMarker(place){
      var marker = new kakao.maps.Marker({
        map: this.map,
        position: new kakao.maps.LatLng(place.y,place.x)
      });

      kakao.maps.event.addListener(marker, 'click', function() {
        // 마커를 클릭하면 장소명이 인포윈도우에 표출됩니다
        this.infowindow.setContent('<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>');
        this.infowindow.open(this.map, marker);
      });
    }
  }

}
</script>

<style scoped>
.map{
  width:100%;
  height:350px;
}
</style>