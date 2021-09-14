<template>
  <div id="map_wrap">
    <div id="map" class="map" style="position:relative;overflow:hidden;"></div>
      <div id="menu_wrap">
        <input type="text" id="keyword" v-model="keyword">
        <button v-on:click="searching">검색</button>
        <ul id="menu" style="margin-left: -1px;float: left;padding: 12px 9px;list-style: none;font-size:20px;line-height: 1.5;position: absolute">
          <li id="search.tab1">
            <a href="#"></a>
            검색</li>
          <li id="search.tab2">
            <a href="#"></a>내가 고른..</li>
        </ul>
        <ul id="placesList" style="margin-left: -1px;float: left;padding: 12px 9px;list-style: none;"></ul>
        <div id="pagination"></div>
      </div>
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

    placeSearchCB(data, status,pagination){
      if(status === kakao.maps.services.Status.OK){
      //  display place and marker
        this.displayPlaces(data)
        this.displayPagination(pagination);

      }else if(status === kakao.maps.services.Status.ZERO_RESULT){
        alert("검색결과가 없습니다.")
      }else if(status === kakao.maps.services.Status.ERROR){
        alert('검색 중 오류가 발생했습니다.')
      }
    },

    displayPlaces(data){
      var listEl = document.getElementById("placesList"),
          menuEl = document.getElementById('menu_wrap'),
          fragment = document.createDocumentFragment(),
          bounds = new kakao.maps.LatLngBounds();

      this.removeAllChildNode(listEl);

      for (let i = 0; i< data.length;i++){
        var itemEl = this.getListItem(i,data[i]);
        this.displayMarker(data[i],i);
        bounds.extend(new kakao.maps.LatLng(data[i].y,data[i].x));

        fragment.appendChild(itemEl);
      }

      listEl.appendChild(fragment);
      menuEl.scrollTop = 0;
      this.map.setBounds(bounds);
    },

    getListItem(index, places){
      const el = document.createElement('id');
      let itemStr = '';

      itemStr = '<span class="markerbg marker_' + (index+1) + '"></span>' +
          '<div class="info">' +
          '   <h5>' + places.place_name + '</h5>';

      if (places.road_address_name) {
        itemStr += '    <span>' + places.road_address_name + '</span>' +
            '   <span class="jibun gray">' +  places.address_name  + '</span>';
      } else {
        itemStr += '    <span>' +  places.address_name  + '</span>';
      }

      itemStr += '  <span class="tel">' + places.phone  + '</span>' +
          '</div>';

      el.innerHTML = itemStr;
      el.className = 'item';

      return el;
    },

    displayMarker(place){
      // var let 으로 하면 인포윈도우 안됨
      const position = new kakao.maps.LatLng(place.y,place.x)
      const marker = new kakao.maps.Marker({
        map: this.map,
        position
      });

      kakao.maps.event.addListener(marker, 'click', () => {
          this.displayInfoWindow(marker,place,position)
          this.map.panTo(position)
      });

    },

    displayPagination(){

    },

    displayInfoWindow(marker,place,position){
      const content = '<div id="infowindow", style="width:180px;height:200px;padding:15px 10px;">' +
          '<span id="placeinfo">'+place.place_name +
          '<p>'+place.phone+'</p>' + '<p>' + place.address_name + '</p>'+'</span>'+
          '<button type="button">닫기</button>'+'</div>'

      let infowindow= new kakao.maps.InfoWindow({
        position,
        content,
        removable:true
      });
      infowindow.setMap(this.map)
    },

    removeAllChildNode(el){
      // console.log(el)
      while (el.hasChildNodes()){
        el.removeChild(el.lastChild)
      }
    },
  }

}
</script>

<style scoped>
body{font: 12px/1.5 'Malgun Gothic','돋움',dotum,sans-serif;}
.map{
  width:100%;
  height:700px;
}
.map_wrap, .map_wrap * {margin:0;padding:0;font-family:'Malgun Gothic',dotum,'돋움',sans-serif;font-size:12px;}
.map_wrap a, .map_wrap a:hover, .map_wrap a:active{color:#000;text-decoration: none;}
.map_wrap {position:relative;width:100%;height:500px;}
.overlay {position:relative;border-color: black;background-color: aqua}
#menu_wrap {position:absolute;top:0;left:0;bottom:0;width:250px;margin:10px 0 30px 10px;padding:5px;overflow-y:auto;background:rgba(255, 255, 255, 0.7);z-index: 1;font-size:12px;border-radius: 10px;}
.bg_white {background:#fff;}
#menu_wrap hr {display: block; height: 1px;border: 0; border-top: 2px solid #5F5F5F;margin:3px 0;}
#menu_wrap .option{text-align: center;}
#menu_wrap .option p {margin:10px 0;}
#menu_wrap .option button {margin-left:5px;}
#placesList li {list-style: none; }
#placesList .item {position:relative;border-bottom:1px solid #888;overflow: hidden;cursor: pointer;min-height: 65px;}
#placesList .item span {display: block;margin-top:4px;}
#placesList .item h5, #placesList .item .info {text-overflow: ellipsis;overflow: hidden;white-space: nowrap}
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