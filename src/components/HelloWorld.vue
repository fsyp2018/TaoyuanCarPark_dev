<template>
  <div>
    <div class="fixed">
    <p class="ml-3 mr-3">提供桃園市各停車場目前相關資訊，此資料由桃園市政府開放資料提供。</p>
    <p class="ml-3 mr-3">排序方式為剩餘車位多到少，綠色代表大於50個車位，紅色代表小於10個車位</p>
    <p style="text-align:right;" class="ml-3 mr-3">最後更新：{{ lastupdate }}</p>
    <!-- 搜尋列 -->
    <!-- 判斷API有沒有傳來areaname，沒有的話則以ID方式 -->
<select  class="custom-select custom-select-lg mb-3" style="text-align:center" v-model="areaname" @change="changearea" v-if="showfilterareaname">
  <option selected>全部</option>
  <option  v-for="(item) in filterareaname" :key="item.id">{{ item }}</option>
</select>
<select  class="custom-select custom-select-lg mb-3" style="text-align:center" v-model="areaId" @change="changearea" v-if="!showfilterareaname">
  <option selected>全部</option>  
  <option value="1">桃園區</option>
  <option value="2">中壢區</option> 
  <option value="3">八德區</option> 
  <option value="4">平鎮區</option> 
  <option value="5">大溪區</option> 
  <option value="6">楊梅區</option> 
  <option value="7">龜山區</option> 
  <option value="8">蘆竹區</option> 
  <option value="11">新屋區</option> 
  <option value="12">龍潭區</option> 
</select>
    </div>

<!-- 列表 -->
<div class="fixedcontent">
<div style="display: flex;flex-direction: row;justify-content:center;flex-wrap:wrap;width:100%;">
<div class="card border-dark  mr-1 ml-1 mt-5 mb-3" style="width: 48%;background-color:#FFC107;" v-for="(item) in filteredcarpark" :key="item.id" :class="{'bg-success':item.surplusSpace >= 50 ,'bg-danger':item.surplusSpace <= 10}">
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
      areaId:'全部',
      lastupdate:'',
      showfilterareaname: true
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
      vm.carpark = response.data.result.records.sort((a,b) => {
        return b.surplusSpace - a.surplusSpace
      })
      vm.filteredcarpark = vm.carpark
      const area = new Array
      for (let i=0; i <　vm.carpark.length; i++){
        area.push(vm.carpark[i].areaName)
        // console.log(vm.area)
      }
        vm.filterareaname = area.filter(function (element, index, arr) {
        return arr.indexOf(element) === index;
    })
        if (vm.filterareaname.length == 1) {
          vm.showfilterareaname = false
        }else{
          vm.showfilterareaname = true
        }
    })
    },
    changearea () {
      // 判斷使用areaname還是areaId
      const vm = this
      if (vm.showfilterareaname){
       if (vm.areaname === '全部') {
      vm.filteredcarpark = Object.assign({}, vm.carpark)
      }else{
      vm.filteredcarpark = vm.carpark.filter(function(item){
      if (item.areaName === vm.areaname){        
          return true
        }
      })
    }
      }else{
       if (vm.areaId === '全部') {
      vm.filteredcarpark = Object.assign({}, vm.carpark)
      }else{
      vm.filteredcarpark = vm.carpark.filter(function(item){
      if (item.areaId === vm.areaId){        
          return true
        }
      })
    }
      }

    },
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
  width: 100%;  
  background-color: white;    
  z-index:10;  
  font-weight:bold;
}
.fixedcontent {  
  position: relative;
  top: 190px;
  width: 100%;   
}
</style>
