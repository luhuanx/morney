<template>
  <layout>
    <div class="tags">
      <router-link class="tag"
                   v-for="tag in tags"
                   :key="tag.id"
                   :to="`/labels/edit/${tag.id}`">
        <span>{{tag.name}}</span>
        <Icon name="right" />
      </router-link>
    </div>
    <div class="createTag-wrapper">
      <Button class="createTag"
              @click="createTag">新增标签</Button>
    </div>
  </layout>
</template>
<script lang = 'ts'>
import { Component } from 'vue-property-decorator'
import Button from '@/components/Button.vue'
import { mixins } from 'vue-class-component'
import TagHelper from '@/mixins/TagHelper'

@Component({
  components: { Button },
})
export default class Labels extends mixins(TagHelper) {
  get tags() {
    return this.$store.state.tagList
  }

  beforeCreate() {
    this.$store.commit('fetchTags')
  }
}
</script>
<style lang = 'scss' scoped>
.tags {
  background: #ffd2d2;
  font-size: 16px;
  padding-left: 16px;
  .tag {
    min-height: 44px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #bcbbc1;
    svg {
      color: #666;
      margin-right: 16px;
      width: 20px;
      height: 20px;
    }
  }
}
.createTag {
  background: #ec648c;
  color: white;
  border-radius: 4px;
  border: none;
  height: 40px;
  padding: 0 16px;
  &-wrapper {
    text-align: center;
    padding: 16px;
    margin-top: 28px;
  }
}
</style>


