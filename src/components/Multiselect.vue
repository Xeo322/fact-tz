<template>
  <div v-if="loading">
    <p>Загрузка</p>
  </div>
  <div v-else>
    <div class="container">
      <div class="item" v-for="(el,i) in filteredData" :key="i">
        <multiselect @select="saveHandler" @remove="saveHandler" :multiple="true" v-model="selected[i]" :options="el"/>
        <SelectedList :list="selected[i]"/>
      </div>
      <div class="pagination">
        <button v-for="(el,i) in history" :key="i" @click="rotateHandler(i)">{{i + 1}}</button>
      </div>
    </div>
    <div v-if="selected.length > 0">
      <button class="reset" @click="resetHandler">Reset</button>
    </div>
    </div>
</template>

<script>
import multiselect from 'vue-multiselect'
import SelectedList from './SelectedList'

export default {
  name: 'HelloWorld',
  data: () => {
    return {
      filteredData: [],
      loading: true,
      error: false,
      selected: [],
      history: []
    }
  },
  components: {
    multiselect,
    SelectedList
  },
  mounted() {
    this.requestHandler()
  },
  methods: {
    async requestHandler() {
      try {
        const request = await fetch('https://raw.githubusercontent.com/WilliamRu/TestAPI/master/db.json')
        const response = await request.json()
        this.filteredData = this.filterHandler(response.testArr)
        this.loading = false
      } catch (e) {
        console.error(e)
      }
    },
    filterHandler(data) {
      let arr = this.flatHandler(data)
      arr = this.primitiveHandler(arr)
      return arr
    },
    flatHandler(arr) {
      let res = [];
      for (let i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
          Array.prototype.push.apply(res, this.flatHandler(arr[i]))
        } else {
          res.push(arr[i])
        }
      }
      return res;
    },
    primitiveHandler(arr) {
      let res = []
      const types = {
        string: 0,
        number: 1,
        object: 2,
      };
      for (let i = 0; i < arr.length; i++) {
        if (this.emptyHandler(arr[i])) {
          if (!res[types[typeof arr[i]]]) {
            res[types[typeof arr[i]]] = new Array(0)
          }
          res[types[typeof arr[i]]].push(arr[i])
        }
      }
      return res
    },
    saveHandler() {
      if (this.history.length === 10) {
        this.history.splice(0,1)
      }
      this.history.push([...this.selected])
    },
    rotateHandler (num) {
      this.selected = this.history[num]
    },
    emptyHandler(obj) {
      return JSON.stringify(obj) !== '{}' && JSON.stringify(obj) !== 'null' && JSON.stringify(obj) !== ''
    },
    resetHandler () {
      this.selected = []
      this.history = []
    }

  }
}
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"/>

<style scoped>
  .container {
    position: relative;
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
  }

  .item {
    flex-basis: 33%;
  }

  .pagination {
    position: fixed;
    display: flex;
    flex-direction: column;
    right: 30px;
    top: 40%;
  }

  .pagination button {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    outline: none;
    border: none;
    background: #5eaeff;
    color: #fafafa;
  }

  .reset {
    width: 100px;
    height: 40px;
    font-size: 20px;
    margin: 0 auto;
    outline: none;
    border: none;
    background: #5eaeff;
    color: #fafafa;
  }
</style>
