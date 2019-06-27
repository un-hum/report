<template>
  <!-- 
  @template list 基于el-table封装的list列表
  @props {String} size 同element-ui的size，参数有mini\small\medium，默认为全局element-ui的默认配置大小
  @props {Array} column 同el-table的字段渲染，支持el-table-column的min-width、width、label、fixed等属性，添加nested属性可用于嵌套th的实现，例子： [{nested: [....]}]
  @props {Array} data 同el-table的数据渲染
  @props {Object} page el-table的页码
  @props {Number} page.now 当前页码
  @props {Number} page.total 总数据数
  @props {Number} page.size 一页显示数据量
  @props {Boolean} selection 是否可选
  @slot 匿名插槽，使用时放在标签对中即可
  -->
  <div>
    <slot name="search"/>
    <slot name="button-group"/>
    <el-table
      @selection-change="handleSelectionChange"
      v-if="column.length"
      class="margin-bottom-10"
      :data="data"
      v-loading="loading"
      ref="multipleTable"
      stripe
      element-loading-text="给我一点时间"
      border
      fit
      highlight-current-row
    >
      <el-table-column type="selection" v-if="selection" />
      <template v-for="(item, index) in column">
        <!-- 用于递归嵌套th -->
        <listItem :column="item" :key="index" v-if="item.nested"/>
        <el-table-column
          v-else
          :min-width="item.minWidth"
          :width="item.width"
          :key="index"
          :label="item.label"
          :align="item.align"
          :fixed="item.fixed || false"
        >
          <template slot-scope="scope">
            <slot v-if="item.slot" :name="item.slot" :row="{row: scope.row, index: scope.$index}"/>
            <span v-else>{{scope.row[item.key]}}</span>
          </template>
        </el-table-column>
      </template>
    </el-table>
    <div class="text-center" v-if="column.length && page">
      <el-pagination
        background
        layout="total, prev, pager, next"
        @current-change="getData"
        :page-size="page.size"
        :total="page.total"
        :current-page.sync="page.now"
      ></el-pagination>
    </div>
    <slot name="content"/>
  </div>
</template>

<script>
import listItem from "./listItem.vue";

export default {
  data() {
    return {
      selected: []
    }
  },
  props: {
    data: {
      default: _ => []
    },
    loading: {
      type: Boolean,
      default: false
    },
    column: {
      default: _ => []
    },
    page: {
      default: _ => null
    },
    selection: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    getSelection() {
      return this.selected
    },
    handleSelectionChange(params) {
      this.selected = params
    },
    getData(p) {
      this.$emit("UPDATE", p);
    }
  },
  components: { listItem }
};
</script>
