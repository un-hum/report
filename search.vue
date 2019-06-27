<template>
  <!-- 
  @template search 用于包裹el-form-item的el-form组件
  @props {String} size 同element-ui的size，参数有mini\small\medium，默认为全局element-ui的默认配置大小
  @props {Boolean} inline 同element-ui的inline，默认为true
  @props {Array} data 用于渲染el-form-item的配置
  @props {Boolean} notSearch 用于取消搜索按钮
  @slot 匿名插槽，使用时放在标签对中即可
  @methods reset 供重置输入框
  @methods setValue 供设置指定搜索框内容 @params {Object} params params.key为要修改对应的key params.value要修改成的内容 必填
  @tip 该template的查询数据格式若以{search: [{key(字段名): xxx, ...}, {key(字段名): xxx}, ....}}]这种方式并且页码的方式以{table: {page: {now: xxx, size: xxx, total: xxx}}}，可直接使用mixins中的getData方法查询数据
  -->
  <el-form :inline="inline" :size="size" ref="form">
    <slot name="before"/>
    <search-item
      :ref="`search_item_${item.key}`"
      v-model="item.value"
      v-for="(item, index) in data"
      v-if="!item.hide"
      :key="index"
      :size="item.size"
      :disabled="item.disabled"
      :width="item.width"
      :label="item.label"
      :type="item.type"
      :selectProps="item.selectProps"
      :selectOptions="item.selectOption"
      :exportProps="item.exportProps"
      :required="item.required"
      :notInline="item.notInline"
      :labelWidth="item.labelWidth"
      :placeholder="item.placeholder"
    />
    <slot name="after"/>
    <slot/>
    <search-item type="6" v-if="notSearch">
      <el-button slot="searchButton" type="primary" @click="getData('form')">搜索</el-button>
    </search-item>
    <slot name="last"/>
  </el-form>
</template>

<script>
import searchItem from "./searchItem";

export default {
  props: {
    size: {
      type: String,
      default: ""
    },
    data: {
      default: _ => []
    },
    notSearch: {
      type: Boolean,
      default: true
    },
    inline: {
      type: Boolean,
      default: true
    }
  },
  methods: {
    setValue(params) {
      let { key, value } = params, target = '';
      if(key == undefined || value== undefined) return console.error('report-search: 请传入需要的值Object {key, value}')      
      this.data.some(e=>{
        if(e.key == key) return target = key
      })
      if(target) {
        this.$refs[`search_item_${key}`][0].handleInput(value)
      }else console.warn('report-search: 在您传入的search列表中不存在您需要改变的key值')
    },
    reset() {
      let index = 0;
      for (let i in this.$refs) {
        if (/search_item_/.test(i)) {
          if (Array.isArray(this.data[index++].key))
            this.$refs[i][0].handleInput([]);
          else this.$refs[i][0].handleInput("");
        }
      }
    },
    getData(formName) {
      // 用于排查是否有比填项没有填写参数
      let _temp = true;
      this.data.some(e => {
        if (e.required) {
          if (Array.isArray(e.key)) {
            if (!e.value.length) return (_temp = false);
          } else {
            if (!e.value) return (_temp = false);
          }
        }
      });
      if (!_temp)
        return this.$message({
          type: "warning",
          message: "有红星标记的为必填项目"
        });
      this.$emit("UPDATE", this.data);
    }
  },
  components: {
    searchItem
  }
};
</script>

