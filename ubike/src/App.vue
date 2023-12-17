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

const uBikeStops = ref([]);
const new_uBikeStops = ref([]);
const keyword = ref('');
const sbiSort = ref(true); // true:小到大、false:大到小
const totSort = ref(true); // true:小到大、false:大到小
const menuLength = ref(20);
const total = ref(1);//總數
const page_Total = ref(1);//總頁數
const page = ref(1);
const start = ref(1);
const end = ref(1);



fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
  });

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};

const filter_uBikeStops = computed(() => {
  // 1. 站點名稱搜尋
  if(!!keyword.value) {
    new_uBikeStops.value = uBikeStops.value.filter(function(item, _) {
      return item.sna.indexOf(keyword.value) > -1;
    });
  } else {
    new_uBikeStops.value = uBikeStops.value;
  }

  total.value = new_uBikeStops.value.length;
  page_Total.value = parseInt(total.value / menuLength.value) === 0 ? 1 : parseInt(total.value / menuLength.value);

  start.value = page.value <= 1 ? 1 : ((page.value -1) * menuLength.value) + 1;
  end.value = page.value <= 1 ? menuLength.value : (page.value * menuLength.value);

  // 3. 加入分頁功能，每頁 20 筆資料
  new_uBikeStops.value = new_uBikeStops.value.filter(function(item, index) {
    index++;
    while(start.value <= index && index <= end.value){
      return true;
    }
    return false;
  });

  return new_uBikeStops.value;
});

// 2. 目前可用車輛 / 總停車格 的排序功能
function sortField(oType, oField) {
  uBikeStops.value = uBikeStops.value.sort(function (a, b) {
    if(oType) {
      return b[oField] - a[oField];//desc
    } else {
      return a[oField] - b[oField];//asc
    }
  });
  return !oType;
};


</script>




<template>
<div class="app w-100">
  <p>
    站點名稱搜尋：<input type="text" v-model="keyword">
  </p>

<nav>
  <ul class="pagination pagination-sm flex-wrap">
    <li class="page-item" :class="page===1 ? 'disabled' : '' ">
      <span class="page-link" @click="(page-1) === 0 ? page=1 : --page">上一頁</span>
    </li>

    <li
      v-for="i in page_Total" :key="i"
      class="page-item" :class="i===page ? 'active' : '' ">
      <span class="page-link" @click="page = i">{{ i }}</span>
    </li>

    <li class="page-item" :class="page===page_Total ? 'disabled' : '' ">
      <span class="page-link"  @click="(page+1) === page_Total ? page=page_Total : ++page">下一頁</span>
    </li>
  </ul>
</nav>


  <table class="table table-striped table-bordered table-sm">
    <thead>
      <tr>
        <th>#</th>
        <th>站點代號</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th style="cursor:pointer;" @click="sbiSort=sortField(sbiSort, 'sbi')">目前可用車輛
          <i v-show="sbiSort" class="fa fa-sort-asc" aria-hidden="true"></i>
          <i v-show="!sbiSort" class="fa fa-sort-desc" aria-hidden="true"></i>
        </th>
        <th style="cursor:pointer;" @click="totSort=sortField(totSort, 'tot')">總停車格
          <i v-show="totSort" class="fa fa-sort-asc" aria-hidden="true"></i>
          <i v-show="!totSort" class="fa fa-sort-desc" aria-hidden="true"></i>
        </th>
        <th>資料更新時間</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(s, index) in filter_uBikeStops" :key="s.sno">
        <td>{{ start+index }}</td>
        <td>{{ s.sno }}</td>
        <td>{{ s.sna }}</td>
        <td>{{ s.sarea }}</td>
        <td>{{ s.sbi }}</td>
        <td>{{ s.tot }}</td>
        <td>{{ timeFormat(s.mday) }}</td>
      </tr>
    </tbody>
  </table>

  <div>顯示第 {{ start }} 至 {{ end }} 項結果，共 {{ total }} 項</div>
</div>

</template>

<style scoped>
.app {
  padding: 1rem;
}
.page-item {
  cursor: pointer;
}
.page-item.disabled {
  cursor: not-allowed;
}
.fa {
  padding: 5px;
}
</style>
