<template>
  <div>
    <label class="formItem">
      <span class="name">{{fieldName}}</span>
      <input type="text"
             :value="value"
             @input="onValueChanged($event.target.value)"
             :placeholder="placeholder">
    </label>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { Component, Prop } from 'vue-property-decorator'

@Component
export default class FormItem extends Vue {
  @Prop({ default: '' }) readonly value!: string

  @Prop({ required: true }) fieldName!: string
  @Prop() placeholder?: string

  onValueChanged(value: string) {
    this.$emit('update:value', value)
  }
}
</script>

<style lang="scss" scoped>
@import '~@/assets/style/helper.scss';
.formItem {
  font-size: 14px;
  color: $color-highlight;
  display: block;
  padding: 0 16px;
  display: flex;
  align-items: center;
  .name {
    padding-right: 16px;
  }
  input {
    height: 40px;
    flex-grow: 1;
    background: transparent;
    border: none;
    color: #fc94b4;
    &::placeholder {
      color: #fc94b4;
    }
  }
}
</style>