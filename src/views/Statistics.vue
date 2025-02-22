/* eslint-disable no-undef */
<template>
  <layout>
    <Tabs class-prefix="type"
          :data-source="recordTypeList"
          :value.sync="type" />
    <ol v-if="groupedList.length>0">
      <li v-for="(group,index) in groupedList"
          :key="index">
        <h3 class="title">{{beautify(group.title)}} <span>￥{{group.total}}</span></h3>
        <ol>
          <li v-for="(item,index) in group.items"
              :key="index"
              class="record">
            <span>{{tagString(item.tags)}}</span>
            <span class="notes">{{item.notes}}</span>
            <span>￥{{item.amount}}</span>
          </li>
        </ol>
      </li>
    </ol>
    <div v-else>
      <img src="../assets/record.png">
      <p class="noResult">暂时还没有记录，快去记一笔吧~</p>

    </div>
  </layout>
</template>

<script lang="ts">
import Vue from 'vue'
import { Component } from 'vue-property-decorator'
import Tabs from '../components/Tabs.vue'
import recordTypeList from '@/constants/recordTypeList'
import dayjs from 'dayjs'
import clone from '@/lib/clone'

@Component({
  components: { Tabs },
})
export default class Statistics extends Vue {
  // eslint-disable-next-line no-undef
  tagString(tags: Tag[]) {
    return tags.length === 0 ? '无' : tags.map((t) => t.name).join('，')
  }

  beautify(string: string) {
    const day = dayjs(string)
    const now = dayjs()
    if (day.isSame(now, 'day')) {
      return '今天'
    } else if (day.isSame(now.subtract(1, 'day'), 'day')) {
      return '昨天'
    } else if (day.isSame(now.subtract(2, 'day'), 'day')) {
      return '前天'
    } else if (day.isSame(now, 'year')) {
      return day.format('M月D日')
    } else {
      return day.format('YYYY年M月D日')
    }
  }

  get recordList() {
    // eslint-disable-next-line no-undef
    return (this.$store.state as RootState).recordList
  }

  get groupedList() {
    const { recordList } = this

    const newList = clone(recordList)
      .filter((r) => r.type === this.type)
      .sort((a, b) => dayjs(b.createAt).valueOf() - dayjs(a.createAt).valueOf())
    if (recordList.length === 0) {
      return []
    }
    type Result = { title: string; total?: number; items: RecordItem[] }[]
    if (!newList.length) return []
    const result: Result = [
      {
        title: dayjs(newList[0].createAt).format('YYYY-MM-DD'),
        items: [newList[0]],
      },
    ]
    for (let i = 1; i < newList.length; i++) {
      const current = newList[i]
      const last = result[result.length - 1]
      if (dayjs(last.title).isSame(dayjs(current.createAt), 'day')) {
        last.items.push(current)
      } else {
        result.push({
          title: dayjs(current.createAt).format('YYYY-MM-DD'),
          items: [current],
        })
      }
    }
    result.map((group) => {
      group.total = group.items.reduce((sum, item) => {
        return sum + item.amount
      }, 0)
    })
    return result
  }

  beforeCreate() {
    this.$store.commit('fetchRecords')
  }

  type = '-'
  recordTypeList = recordTypeList
}
</script>

<style scoped lang="scss">
img {
  display: block;
  width: 100px;
  height: 100px;
  margin-left: auto;
  margin-right: auto;
  margin-top: 100px;
}
.noResult {
  padding: 16px;
  text-align: center;
}
%item {
  padding: 8px 16px;
  line-height: 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.title {
  @extend %item;
}
.record {
  @extend %item;
  background: #ffd2d2;
}
.notes {
  margin-right: auto;
  margin-left: 16px;
  color: #fc94b4;
}
::v-deep {
  .type-tabs-item {
    background: #fcccd4;
    &.selected {
      background: #f40463;
      color: white;
      &::after {
        display: none;
      }
    }
  }
  .interval-tabs-item {
    height: 48px;
  }
}
</style>