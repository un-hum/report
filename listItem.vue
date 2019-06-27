<template>
    <el-table-column
      :min-width="column.minWidth"
      :width="column.width"
      :label="column.label"
      :align="column.align"
      :fixed="column.fixed || false"
    >
      <template v-for="(item, index) in column.nested">
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
            <slot
              v-if="item.slot"
              :name="item.slot"
              :row="{row: scope.row, index: scope.$index}"
            />
            <span v-else>{{scope.row[item.key]}}</span>
          </template>
        </el-table-column>
      </template>
    </el-table-column>
</template>

<script>
export default {
  name: "listItem",
  props: ["column"]
};
</script>
