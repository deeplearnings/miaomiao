<template>
  <div class="city_body">
    <div class="city_list">
      <div class="city_hot">
        <h2>热门城市</h2>
        <ul class="clearfix">
          <li v-for="(city, index) in hotCities" :key="index">{{ city }}</li>
        </ul>
      </div>
      <div class="city_sort" ref="city_sort">
        <div v-for="item in sortedCityKeys" :key="item">
          <h2>{{ item }}</h2>
          <ul>
            <li v-for="city in cityList[item]" :key="city.id">{{ city.nm }}</li>
          </ul>
        </div>
      </div>
    </div>
    <div class="city_index">
      <ul>
        <li
          v-for="(item, index) in sortedCityKeys" :key="item" @touchstart="movePos(index)">
          {{ item }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "City",
  data() {
    return {
      hotCities: ["北京", "上海", "天津", "重庆", "深圳", "广州"],
      cityList: {}
    };
  },
  computed: {
    sortedCityKeys() {
      //获取cityList json对象的key值数组
      return Object.keys(this.cityList).sort((a, b) => {
        var ret = 0;
        if (a < b) {
          ret = -1;
        }
        if (a > b) {
          ret = 1;
        }
        return ret;
      });
    }
  },
  mounted() {
    this.axios.get("/api/dianying/cities.json").then(res => {
      var statusTxt = res.statusText;
      if (statusTxt === "OK") {
        var cities = res.data.cts;
        this.cityList = this.formatCityList(cities);
      }
    });
  },
  methods: {
    formatCityList(cities) {
      var cityList = {};
      for (let index = 0; index < cities.length; index++) {
        var firstLetter = cities[index].py.substring(0, 1).toUpperCase();
        if (this.isInKeys(firstLetter, cityList)) {
          //如果存在字母索引，则将当前城市加入
          cityList[firstLetter].push(cities[index]);
        } else {
          //如果不存在字母索引，则将新增索引
          cityList[firstLetter] = [cities[index]];
        }
      }
      return cityList;
    },
    isInKeys(key, objJson) {
      var boolValue = false;
      for (var n in objJson) {
        if (key === n) {
          boolValue = true;
          break;
        }
      }
      return boolValue;
    },
    movePos(index) {
      var h2Arr = this.$refs.city_sort.getElementsByTagName("h2");
      this.$refs.city_sort.parentNode.scrollTop = h2Arr[index].offsetTop;
    }
  }
};
</script>

<style scoped>
.city_body {
  margin-top: 45px;
  display: flex;
  width: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
}
.city_body .city_list {
  flex: 1;
  overflow: auto;
  background: #fff5f0;
}
.city_body .city_list::-webkit-scrollbar {
  background-color: transparent;
  width: 0;
}
.city_body .city_hot {
  margin-top: 20px;
}
.city_body .city_hot h2 {
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal;
}
.city_body .city_hot ul li {
  float: left;
  background: #fff;
  width: 29%;
  height: 33px;
  margin-top: 15px;
  margin-left: 3%;
  padding: 0 4px;
  border: 1px solid #e6e6e6;
  border-radius: 3px;
  line-height: 33px;
  text-align: center;
  box-sizing: border-box;
}
.city_body .city_sort div {
  margin-top: 20px;
}
.city_body .city_sort h2 {
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal;
}
.city_body .city_sort ul {
  padding-left: 10px;
  margin-top: 10px;
}
.city_body .city_sort ul li {
  line-height: 30px;
}
.city_body .city_index {
  width: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  border-left: 1px #e6e6e6 solid;
}
</style>
