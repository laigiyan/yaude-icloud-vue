<template>
  <div class="config-list">
    <a-radio-group v-model="type">
      <div class="item">
        <a-radio value="TYPE_NOT_SET" class="choice" :disabled="disableChoice">不設置</a-radio>
        <span class="tip-info">日和週只能設置其中之一</span>
      </div>
      <div class="item">
        <a-radio value="TYPE_RANGE" class="choice" :disabled="disableChoice">區間</a-radio>
        從
        <a-select v-model="valueRange.start" class="w80" :disabled="type!==TYPE_RANGE || disableChoice">
          <template v-for="(v, k) of WEEK_MAP">
            <a-select-option :value="v">{{k}}</a-select-option>
          </template>
        </a-select>
        至
        <a-select v-model="valueRange.end" class="w80" :disabled="type!==TYPE_RANGE || disableChoice">
          <template v-for="(v, k) of WEEK_MAP">
            <a-select-option :value="v">{{k}}</a-select-option>
          </template>
        </a-select>
      </div>
      <div class="item">
        <a-radio value="TYPE_LOOP" class="choice" :disabled="disableChoice">循環</a-radio>
        從
        <a-select v-model="valueLoop.start" class="w80" :disabled="type!==TYPE_LOOP || disableChoice">
          <template v-for="(v, k) of WEEK_MAP">
            <a-select-option :value="v">{{k}}</a-select-option>
          </template>
        </a-select>
        開始，間隔
        <a-input-number :disabled="type!==TYPE_LOOP || disableChoice" :max="maxValue" :min="minValue" :precision="0" class="w60" v-model="valueLoop.interval"/>
        天
      </div>
      <div class="item">
        <a-radio value="TYPE_SPECIFY" class="choice" :disabled="disableChoice">指定</a-radio>
        <div class="list">
          <a-checkbox-group v-model="valueList">
            <template v-for="i in specifyRange">
              <a-checkbox class="list-check-item" :key="`key-${i}`" :value="i" :disabled="type!==TYPE_SPECIFY || disabled">{{i}}</a-checkbox>
            </template>
          </a-checkbox-group>
        </div>
      </div>
    </a-radio-group>
  </div>
</template>

<script>
import mixin from './mixin'
import { replaceWeekName, WEEK_MAP_EN } from './const.js'

const WEEK_MAP = {
  '週一': 1,
  '週二': 2,
  '週三': 3,
  '週四': 4,
  '週五': 5,
  '週六': 6,
  // 按照國人習慣，將週日放到每週的最后一天
  '週日': 7,
}

export default {
  name: 'week',
  mixins: [mixin],
  props: {
    day: {
      type: String,
      default: '*'
    }
  },
  data() {
    return {
      WEEK_MAP,
      WEEK_MAP_EN
    }
  },
  computed: {
    disableChoice() {
      return (this.day && this.day !== '?') || this.disabled
    }
  },
  watch: {
    value_c(newVal, oldVal) {
      // 如果設置日，那么星期就直接不設置
      this.updateValue()
    },
    day(newVal) {
      // console.info('new day: ' + newVal)
      this.updateValue()
    }
  },
  methods: {
    updateValue() {
      this.$emit('change', this.disableChoice ? '?' : this.value_c)
    },
    preProcessProp(c) {
      return replaceWeekName(c)
    }
  },
  created() {
    this.DEFAULT_VALUE = '*'
    // 0,7表示週日 1表示週一
    this.minValue = 1
    this.maxValue = 7
    this.valueRange.start = 1
    this.valueRange.end = 7
    this.valueLoop.start = 2
    this.valueLoop.interval = 1
    this.parseProp(this.prop)
  }
}
</script>

<style lang="less" scoped>
  @import "mixin.less";
</style>
