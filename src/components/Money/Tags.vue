<template>
  <div class="tags">
    <div class="new">
      <button @click="createTag">
        <Icon name="add" />
      </button>
    </div>
    <ul class="current">
      <li v-for="tag in tagList"
          :key="tag.id"
          :class="{selected: selectedTags.indexOf(tag)>=0}"
          @click="toggle(tag)">{{tag.name}}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import { Component } from 'vue-property-decorator'
import { mixins } from 'vue-class-component'
import TagHelper from '@/mixins/TagHelper'

@Component
export default class Tags extends mixins(TagHelper) {
  selectedTags: string[] = []

  get tagList() {
    return this.$store.state.tagList
  }

  created() {
    this.$store.commit('fetchTags')
  }

  toggle(tag: string) {
    const index = this.selectedTags.indexOf(tag)
    if (index >= 0) {
      this.selectedTags.splice(index, 1)
    } else {
      this.selectedTags.push(tag)
    }
    this.$emit('update:value', this.selectedTags)
  }
}
</script>

<style lang="scss" scoped>
@use 'sass:math';
.tags {
  flex-grow: 1;
  font-size: 14px;
  padding: 16px;
  background-color: #fff;
  display: flex;
  flex-direction: column-reverse;
  > .current {
    display: flex;
    flex-wrap: wrap;
    > li {
      font-size: 16px;
      $bg: #f4cccc;
      background: $bg;
      $h: 24px;
      height: $h;
      line-height: $h;
      border-radius: math.div($h, 2);
      padding: 0 16px;
      margin-right: 12px;
      margin-top: 4px;
      &.selected {
        background: darken($bg, 20%);
        color: #ffe9e9;
      }
    }
  }
  > .new {
    padding-top: 16px;
    button {
      background: transparent;
      border: none;
      color: #e35084;
      padding: 0 10px;
    }
    .icon {
      width: 24px;
      height: 24px;
    }
  }
}
.layout-content {
  display: flex;
  flex-direction: column-reverse;
}
</style>