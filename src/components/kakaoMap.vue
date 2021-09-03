<template>
  <div>
    <div id="map" class="map" style="position:relative;overflow:hidden;"></div>
      <div id="menu_wrap">
        <input type="text" id="keyword" v-model="keyword">
        <button v-on:click="searching">검색</button>
      </div>
    <ul id="placesList"></ul>
    <div id="pagination"></div>
  </div>
</template>

<script>
export default {
  name: "KakaoMap",
  data() {
    return {
      map : null,
      keyword: '',
      // infowindow : null
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

        for (let i = 0; i< data.length;i++){
          this.displayMarker(data[i]);
          bounds.extend(new kakao.maps.LatLng(data[i].y,data[i].x))
        }
        this.displayPagination(pagination);

      }else if(status === kakao.maps.services.Status.ZERO_RESULT){
        alert("검색결과가 없습니다.")
      }else if(status === kakao.maps.services.Status.ERROR){
        alert('검색 중 오류가 발생했습니다.')
      }

      this.map.setBounds(bounds);
    },

    displayMarker(place){


      // var let 으로 하면 인포윈도우 안됨
      const marker = new kakao.maps.Marker({
        map: this.map,
        position: new kakao.maps.LatLng(place.y,place.x)
      });

      const content = '<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>'
      let infowindow = new kakao.maps.InfoWindow({
        // map: this.map,
        // position: kakaoposition,
        removable:true,
        content
      });
      // infowindow.open(this.map, marker);
      // console.log(place.place_name)

      kakao.maps.event.addListener(marker, 'click', () => {
        infowindow.open(this.map,marker);
      });

      // kakao.maps.event.addListener(marker,'',function (){
      //   infowindow.close();
      // })
    },
    displayPagination(){

    }
  }
}
</script>

<style scoped>
.map{
  width:100%;
  height:350px;
}
.map_wrap, .map_wrap * {margin:0;padding:0;font-family:'Malgun Gothic',dotum,'돋움',sans-serif;font-size:12px;}
.map_wrap a, .map_wrap a:hover, .map_wrap a:active{color:#000;text-decoration: none;}
.map_wrap {position:relative;width:100%;height:500px;}
#menu_wrap {position:absolute;top:0;left:0;bottom:0;width:250px;margin:10px 0 30px 10px;padding:5px;overflow-y:auto;background:rgba(255, 255, 255, 0.7);z-index: 1;font-size:12px;border-radius: 10px;}
.bg_white {background:#fff;}
#menu_wrap hr {display: block; height: 1px;border: 0; border-top: 2px solid #5F5F5F;margin:3px 0;}
#menu_wrap .option{text-align: center;}
#menu_wrap .option p {margin:10px 0;}
#menu_wrap .option button {margin-left:5px;}
#placesList li {list-style: none;}
#placesList .item {position:relative;border-bottom:1px solid #888;overflow: hidden;cursor: pointer;min-height: 65px;}
#placesList .item span {display: block;margin-top:4px;}
#placesList .item h5, #placesList .item .info {text-overflow: ellipsis;overflow: hidden;white-space: nowrap;}
#placesList .item .info{padding:10px 0 10px 55px;}
#placesList .info .gray {color:#8a8a8a;}
#placesList .info .jibun {padding-left:26px;background:url(https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/places_jibun.png) no-repeat;}
#placesList .info .tel {color:#009900;}
#placesList .item .markerbg {float:left;position:absolute;width:36px; height:37px;margin:10px 0 0 10px;background:url(https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/marker_number_blue.png) no-repeat;}
#placesList .item .marker_1 {background-position: 0 -10px;}
#placesList .item .marker_2 {background-position: 0 -56px;}
#placesList .item .marker_3 {background-position: 0 -102px}
#placesList .item .marker_4 {background-position: 0 -148px;}
#placesList .item .marker_5 {background-position: 0 -194px;}
#placesList .item .marker_6 {background-position: 0 -240px;}
#placesList .item .marker_7 {background-position: 0 -286px;}
#placesList .item .marker_8 {background-position: 0 -332px;}
#placesList .item .marker_9 {background-position: 0 -378px;}
#placesList .item .marker_10 {background-position: 0 -423px;}
#placesList .item .marker_11 {background-position: 0 -470px;}
#placesList .item .marker_12 {background-position: 0 -516px;}
#placesList .item .marker_13 {background-position: 0 -562px;}
#placesList .item .marker_14 {background-position: 0 -608px;}
#placesList .item .marker_15 {background-position: 0 -654px;}
#pagination {margin:10px auto;text-align: center;}
#pagination a {display:inline-block;margin-right:10px;}
#pagination .on {font-weight: bold; cursor: default;color:#777;}
</style>