<script setup>
  import { ref, computed, watch } from 'vue';

  // 修改這份 YouBike 即時資訊表，並加上
  // 1. 站點名稱搜尋
  // 2. 目前可用車輛 / 總停車格 的排序功能
  // 3. 加入分頁功能，每頁 20 筆資料

  // 欄位說明:
  // https://data.taipei/dataset/detail?id=c6bc8aed-557d-41d5-bfb1-8da24f78f2fb
  // sno：站點代號、 sna：場站名稱(中文)、 tot：場站總停車格、
  // sbi：場站目前車輛數量、 sarea：場站區域(中文)、 mday：資料更新時間、
  // lat：緯度、 lng：經度、 ar：地(中文)、 sareaen：場站區域(英文)、
  // snaen：場站名稱(英文)、 aren：地址(英文)、 bemp：空位數量、 act：全站禁用狀態
  const temp = ref(false);
  const uBikeStops = ref([]);                           //接收資料庫站點資料
  const searchInput = ref("");  
                          //接收使用者輸入值
  const sortBike = ref(false);                    //可用車輛sort被點擊                
  const sortParking = ref(false); 

  const tableSelect = ref('');                    //總停車格sort被點擊
  const tableCode = ref('');

  fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
    .then(res => res.text())
    .then(data => {
      uBikeStops.value = JSON.parse(data);
    });
  
  
  const filUBikeStops = computed(()=>{    
    
    let arr = ref([]);
    const tableCode = ref(tableCode.value);
    
    //篩選關鍵字
    arr = uBikeStops.value.filter((stop)=>{
      return stop.sna.indexOf(searchInput.value) !== -1;
    });

    //如果”可用車輛“被點擊
    // if (sortBike.value == true) {
    //   arr = arr.slice().sort((a, b) => {
    //     return a.sbi - b.sbi;
    //   });
    // } else {
    //   arr = arr.slice().sort((a, b) => {
    //     return b.sbi - a.sbi;
    //   });
    // }

    
    arr = arr.slice().sort((a, b) => {
      return a[type] - b[type];
    });
    

    return arr;
  });

  //搜集點擊的 table head
  const selectTable = ((tableName) => {
    tableSelect.value = tableName;
    if (tableSelect.value == 'bikes') {
      tableCode.value = 'sbi'
    } else if (tableSelect.value == 'space') {
      tableCode.value = 'tot'
    }
    console.log(tableSelect.value);
  })

  const sortResult = ((tableCode)=>{

    if (condition) {
      
    }
    
      return direction === true ?
      (a, b) => a[tableCode] - b[tableCode] :
      (a, b) => b[tableCode] - c[tableCode]
    
  })



  //如果”可用車輛“被點擊
  // watch (sortBike, (value)=>{
  //   console.log('watch',value);
  //   if (value == true) {
  //     filUBikeStops = filUBikeStops.value.slice().sort((a, b) => {
  //       return a.sbi - b.sbi;
  //     });
  //   }
  //   if (value == false) {
  //     filUBikeStops = filUBikeStops.value.slice().sort((a, b) => {
  //       return b.sbi - a.sbi;
  //     });
  //   }
  // })

  //如果“總停車格”被點擊
  // watch(sortParking, (value) => {
  //   console.log('watch', value);
  //   if (value == true) {
  //     filUBikeStops = filUBikeStops.value.slice().sort((a, b) => {
  //       return a.tot - b.tot;
  //     });
  //   }
  //   if (value == false) {
  //     filUBikeStops = filUBikeStops.value.slice().sort((a, b) => {
  //       return b.tot - a.tot;
  //     });
  //   }
  // })

  
  
  const timeFormat = (val) => {               // 時間格式
    const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
    return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
  };
</script>

<template>
  <div class="app">
    <p>
      站點名稱搜尋: <input type="text" v-model="searchInput">
    </p>

    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th @click="selectTable('bikes')" style="cursor:pointer;">目前可用車輛
            <i class="fa fa-sort-asc" aria-hidden="true"></i>
            <i class="fa fa-sort-desc" aria-hidden="true"></i>
          </th>
          <th @click="selectTable('space')" style="cursor:pointer;">總停車格
            <i class="fa fa-sort-asc" aria-hidden="true"></i>
            <i class="fa fa-sort-desc" aria-hidden="true"></i>
          </th>
          <th>資料更新時間</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="s in filUBikeStops" :key="s.sno">
          <td>{{ s.sno }}</td>
          <td>{{ s.sna }}</td>
          <td>{{ s.sarea }}</td>
          <td>{{ s.sbi }}</td>
          <td>{{ s.tot }}</td>
          <td>{{ timeFormat(s.mday) }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
  .app {
    padding: 1rem;
  }
</style>