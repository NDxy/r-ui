<template>
  <div class="r-search">
    <div v-if="isShowSearch" class="r-search-content container-fluid">
      <div class="head row">
        <form action="" class="search-input col-xs-10">
          <i class="icon-search"></i>
          <div class="input"></div>
        </form>
        <div class="col-xs-2" @click="closeSearch">
          <div class="search-cancle">
            <span>取消</span>
          </div>
        </div>
      </div>
      <div class="result">
        <div v-show="list.length <= 0" class="row tip">
          <div class="col-xs-6 word">{{title}}</div>
          <div v-show="history.length > 0" class="col-xs-6" @click="clear">
            <div class="icon">
              <i class="icon-del"></i>
            </div>
          </div>
        </div>
        <div class="list" :class="{'b-border': list.length > 0 || history.length > 0}">
          <div v-if="list.length > 0">
            <div v-for="(item, index) in list" :key="index" class="item" :class="{'last': index === list.length - 1}" @click="search(item)">{{item}}</div>
          </div>
          <div v-if="list.length <= 0 && history.length > 0">
            <div v-for="(item, index) in history" :key="index" class="item" :class="{'last': index === history.length - 1}" @click="search(item)">{{item}}</div>
          </div>
        </div>
      </div>
    </div>
    <form action="" class="r-search-control" @submit.prevent="search(keyword)">
      <i class="icon-search"></i>
      <input ref="keyword"
        class="r-search-placeholder"
        @focus="showSearch"
        :class="{'r-search-input': isShowSearch}" type="search"
        v-model="keyword"
        maxlength="20"
        :placeholder="defaultPlaceholder"/>
    </form>
  </div>
</template>

<script>
import store from 'store'
import rPopup from './../popup/index.vue'

export default{
  props: {
    defaultPlaceholder: {
      type: String,
      default: '请输入'
    },
    searchWord: {
      type: String,
      default: ''
    },
    list: {
      type: Array,
      default: []
    }
  },
  data () {
    return {
      title: '最近搜索',
      keyword: '',
      isShowSearch: false,
      direction: 'bottom',
      history: store.get('searchList') || []
    }
  },
  watch: {
    keyword (val) {
      this.$emit('input', val)
    },
    isShowSearch (val) {
      if (val) {
        this.history = this.$R.take(10)(this.history)
      }
    },
    searchWord () {
      if (this.searchWord !== this.keyword) {
        this.keyword = this.searchWord
      }
    }
  },
  components: {rPopup},
  methods: {
    showSearch (e) {
      this.isShowSearch = true
    },
    closeSearch () {
      this.$refs.keyword.blur()
      this.isShowSearch = false
      this.$emit('close')
    },
    clearInput () {
      this.keyword = ''
      this.list = []
    },
    clear () {
      this.history = []
      store.remove('searchList')
    },
    search (item) {
      this.keyword = item
      if (this.history.length > 0 && this.history.indexOf(item) >= 0) {
        this.$emit('select', item)
        this.closeSearch()
      } else {
        if (item !== '') {
          this.history = this.$R.insert(0, item)(this.history)
          store.set('searchList', this.history)
        }
        this.$emit('select', item)
        this.closeSearch()
      }
    }
  },
  created () {
    this.keyword = this.searchWord
  }
}
</script>

<style lang="scss">
@import "./style.scss";
</style>
