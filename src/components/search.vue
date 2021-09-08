<template>
  <div>
    <input type="text" v-model="keyword" id="keyword">
    <button v-on:click="searching" @keyup.enter="searching">검색</button>
    <choiceList></choiceList>
    <placeList></placeList>
  </div>
</template>

<script>
import choiceList from "@/components/search/choiceList.vue"
import placeList from "@/components/search/placeList.vue";
export default {
  name: "search",
  data() {
    return {
      keyword: '',
      placedata: null
    }
  },
  methods:{
    searching:function (){
      var ps = new kakao.maps.services.Places();
      ps.keywordSearch(this.keyword, this.placeSearchCB)
    },

    // eslint-disable-next-line no-unused-vars
    placeSearchCB(data, status, pagination) {
      if(status === kakao.maps.services.Status.OK){
        this.placedata = data
      }else if(status === kakao.maps.services.Status.ZERO_RESULT){
        alert("검색결과가 없습니다.")
      }else if(status === kakao.maps.services.Status.ERROR){
        alert('검색 중 오류가 발생했습니다.')
      }
    },

  },
  components:{
    choiceList,
    placeList
  }
}
</script>

<style scoped>


</style>