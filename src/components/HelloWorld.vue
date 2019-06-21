<template>
  <div>
    <div class="fixed">
    <p>提供桃園市各停車場目前相關資訊，此資料由桃園市政府開放資料提供</p>
    <p>最後更新：{{ lastupdate }}</p>
    <!-- 搜尋列 -->
<select  class="custom-select custom-select-lg mb-3" style="text-align:center" v-model="areaname" @change="changearea">
  <option selected>全部</option>
  <option  v-for="(item) in filterareaname" :key="item.id" >{{ item}}</option>  
</select>
    </div>

<!-- 列表 -->
<div class="fixedcontent">
<div style="display: flex;flex-direction: row;justify-content:center;flex-wrap:wrap;">
<div class="card border-dark mb-3 mr-3 ml-3" style="width: 40%;" v-for="(item) in filteredcarpark" :key="item.id" :class="{'bg-success':item.surplusSpace >= 50 ,'bg-danger':item.surplusSpace <= 10}">
  <div class="card-header bg-transparent border-dark">{{ item.parkName}}</div>
  <div class="card-body text-dark">
    <h5 class="card-title">停車場地址</h5>
    <p class="card-text ">{{ item.address}}</p>
    <h5 class="card-title text-dark">收費方式</h5>
    <p class="card-text">{{ item.payGuide}}</p>
  </div>
  <div class="card-footer bg-transparent border-dark">總車位:{{ item.totalSpace}}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;剩餘車位:{{ item.surplusSpace}}</div>  
</div>
</div>
</div>

  </div>
</template>

<script>
export default {
  data () {
    return {
      carpark:[],
      filterareaname:{},
      filteredcarpark:[],
      areaname:'全部',
      lastupdate:'',
      test:[]
    }
  },
  methods:{
    getData (){
    const vm =this
    const cors = 'https://cors-anywhere.herokuapp.com/';
    const api = `https://data.tycg.gov.tw/api/v1/rest/datastore/0daad6e6-0632-44f5-bd25-5e1de1e9146f?format=json`;
    this.$http.get(cors+api).then(response => {
      // console.log(response)
      vm.lastupdate = new Date(response.headers.date)      
      vm.carpark = response.data.result.records
      vm.filteredcarpark = response.data.result.records
      const area = new Array
      for (let i=0; i <　vm.carpark.length; i++){
        area.push(vm.carpark[i].areaName)
        // console.log(vm.area)
      }
        vm.filterareaname = area.filter(function (element, index, arr) {
        return arr.indexOf(element) === index;
    });

    });
    },
    changearea () {
      const vm = this
      if (vm.areaname === '全部') {
      vm.filteredcarpark = Object.assign({}, vm.carpark)
      }else{
      vm.filteredcarpark =vm.carpark.filter(function(item){
      if (item.areaName === vm.areaname){        
          return true
        }
      })
    }
    }
  },
  created () {
    const vm =this
    vm.getData()
  }
}
</script>

<style scoped lang="scss">
.fixed {
  position: fixed;
  top: 200;
  width: 100%;  
  background-color: white;
  z-index:10;
}
.fixedcontent {
  position: relative;
  top: 170px;
  width: 100%;  
}
</style>
