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
  
  /*===============
    接收資料庫站點資料
  ================*/
  const uBikeStops = ref([]); 

  fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
  });
  
  /*====================
    搜集點擊的 table head
  ====================*/
  const tableSelect = ref('');  //紀錄選中的 table head
  const tableCode = ref('');    //紀錄 onClick 後 table head code
  const direction = ref(false); //true:升冪a-b, false:降冪b-a
  
  /**
   * Function selectTableHead
   * 會接收到使用者點擊到 table head 的名稱
   * 並依返回的名稱設定 tableCode.value
   * 同時反轉 tableSelect 的值
   * 
   * @param {string}tableName - 帶入table名稱
   */
  const selectTableHead = (tableName) => {
    tableSelect.value = tableName;
    direction.value = !direction.value;
    if (tableSelect.value == 'bikes') {
      tableCode.value = 'sbi'
    } else if (tableSelect.value == 'space') {
      tableCode.value = 'tot'
    }
    console.log('direction.value', direction.value);
  }

  
  /*======================
    計算屬性 - 計算過濾後的值
  ======================*/
  const searchInput = ref(""); //搜集input資料

  /**
   * Function sortDirection
   * 帶入 tableCode 參數
   * 判斷 direction 的值並返回 .sort 用的參數
   * 
   * @param {string}tableCode - 輸入table名稱
   * @return - 返回.sort 用的參數
   */
  const sortDirection = (tableCode) => {
    return direction.value === true ?
      (a, b) => a[tableCode] - b[tableCode] :
      (a, b) => b[tableCode] - a[tableCode]
  }
  const filUBikeStops = computed(()=>{    
    
    let arr = ref([]);
    if (tableCode.value) {
      console.log('tableCode.value 有值進來了',tableCode.value);
    }
    
    //篩選關鍵字
    arr = uBikeStops.value.filter((stop)=>{
      return stop.sna.indexOf(searchInput.value) !== -1;
    });

    //排序
    arr = uBikeStops.value.sort(sortDirection(tableCode.value));

    return arr;
  });

  
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
          <th @click="selectTableHead('bikes')" style="cursor:pointer;">目前可用車輛
            <i class="fa fa-sort-asc" aria-hidden="true"></i>
            <i class="fa fa-sort-desc" aria-hidden="true"></i>
          </th>
          <th @click="selectTableHead('space')" style="cursor:pointer;">總停車格
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