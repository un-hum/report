<template>
  <!-- 
  @template searchItem el-form-item包裹好的各种element-ui下的表单组件
  @props {String} size 同element-ui的size，参数有mini\small\medium，默认为全局element-ui的默认配置大小
  @props {String, Number} type 选择使用的交互组件，0: input输入框 1: select 2: 单日期选择 3: 多日期选择 4: 导出按钮 5: 使用匿名插槽，6: 单选 7: textarea模式的输入框 （大于这些为具名插槽）
  @props {String} width 设置form-item宽度
  @props {Object} selectProps 当type为1时间，用于配置select的option的选择项和显示项
  @props {String} selectProps.value 选择option后返回的value值，默认为'value'
  @props {String} selectProps.label option选项的显示对应的绑定字段，默认为'label'
  @props {String} label 同element-ui el-form-item的label，用于显示字段名称
  @props {String} labelWidth 设置label宽度
  @props {Boolean} disabled 设置是否禁用
  @props {String} placeholder 字面意思
  @props {Boolean} notInline 是否使当前el-form-item拥有换行属性（display: block）
  @props {Boolean} required 用于限制搜索条件必填
  @props {Array} selectOptions 用于存放option列表数据 
  @props {Object} exportProps 当type为4时间，用于配置导出文件的url和请求body
  @props {String} exportProps.url 放置导出文件的url
  @props {Object} exportProps.body 放置导出文件的请求体
  @slot 匿名插槽，使用时放在标签对中即可
  -->
  <el-form-item
    :size="size"
    :style="`width: ${width};${notInline ? 'display: block' : ''}`"
    :label="label"
    :label-width="labelWidth"
    :rules="required ? rules : null"
  >
    <!-- 输入框 -->
    <el-input
      :disabled="disabled"
      v-if="type == 0"
      :placeholder="placeholder"
      :value="content"
      @input="handleInput($event)"
    />
    <!-- 下拉选择 -->
    <el-select
      :disabled="disabled"
      v-if="type == 1"
      clearable
      :value="content"
      @input="handleInput($event)"
      placeholder="请选择"
    >
      <el-option
        v-for="(item, index) in selectOptions"
        :key="index"
        :label="item[selectProps.label]"
        :value="item[selectProps.value]"
      />
    </el-select>
    <!-- 单日期选择 -->
    <el-date-picker
      :disabled="disabled"
      :editable="false"
      value-format="yyyy-MM-dd HH:mm:ss"
      format="yyyy-MM-dd HH:mm:ss"
      v-if="type == 2"
      @input="handleInput($event)"
      :value="content"
      type="datetime"
      placeholder="请选择日期时间"
      default-time="00:00:00"
    />
    <!-- 多日期选择 -->
    <el-date-picker
      :disabled="disabled"
      :editable="false"
      value-format="yyyy-MM-dd HH:mm:ss"
      format="yyyy-MM-dd HH:mm:ss"
      :value="content"
      v-if="type == 3"
      @input="handleInput($event)"
      type="daterange"
      range-separator="至"
      start-placeholder="开始时间"
      end-placeholder="结束时间"
    />
    <!-- 导出 -->
    <el-button :disabled="disabled" v-if="type == 4" type="primary" @click="download">导出</el-button>
    <!-- 下拉选择 -->
    <el-radio-group
      :disabled="disabled"
      v-if="type == 6"
      clearable
      :value="content"
      @input="handleInput($event)"
      placeholder="请选择"
    >
      <el-radio
        v-for="(item, index) in selectOptions"
        :key="index"
        :label="item[selectProps.value]"
      >{{item[selectProps.label]}}</el-radio>
    </el-radio-group>
    <!-- textarea输入框 -->
    <el-input
      :disabled="disabled"
      v-if="type == 7"
      type="textarea"
      :placeholder="placeholder"
      :value="content"
      @input="handleInput($event)"
    />
    <!-- 输入框 -->
    <el-input-number
      :value="content"
      v-if="type == 8"
      :disabled="disabled"
      @input="handleInput($event)"
    />
    <!-- 匿名插槽配置 -->
    <slot v-if="type == 5"/>
    <!-- 具名插槽search -->
    <slot name="searchButton"/>
  </el-form-item>
</template>

<script>
import downloadFile from "@/utils/postDownload";

export default {
  data() {
    return {
      content: null,
      rules: [{ required: true, trigger: "blur" }]
    };
  },
  props: {
    placeholder: {
      type: String,
      default: '请添加内容'
    },
    disabled: {
      type: Boolean,
      default: false
    },
    labelWidth: {
      type: String,
      default: ""
    },
    notInline: {
      type: Boolean,
      default: false
    },
    required: {
      type: Boolean,
      default: false
    },
    type: {
      type: [Number, String],
      default: "0"
    },
    size: {
      type: String,
      default: ""
    },
    width: {
      type: String,
      default: "auto"
    },
    // select-option字段配置
    selectProps: {
      default: _ => ({
        value: "value",
        label: "label"
      })
    },
    label: {
      type: String,
      default: ""
    },
    selectOptions: {
      default: _ => []
    },
    exportProps: {
      default: _ => ({
        url: "",
        body: {}
      })
    }
  },
  methods: {
    download() {
      downloadFile({
        url: this.exportProps.url,
        body: this.exportProps.body
      });
    },
    handleInput(params) {
      this.content = params;
      this.$emit("input", params);
    }
  }
};
</script>

<style lang="sass" scoped>
.container
  display: inline-block
</style>


