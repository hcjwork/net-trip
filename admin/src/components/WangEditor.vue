<template lang="html">
  <div class="editor">
    <div ref="toolbar" class="toolbar"></div>
    <div ref="editor" class="text"></div>
  </div>
</template>

<script>
import E from 'wangeditor'
export default {
  name: 'editoritem',
  data() {
    return {
      // uploadPath,
      editor: null,
      info_: null
    }
  },
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    isClear: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    isClear(val) {
      // 触发清除文本域内容
      if (val) {
        this.editor.txt.clear()
        this.info_ = null
      }
    },
    value: function (value) {
      if (value !== this.editor.txt.html()) {
        this.editor.txt.html(this.value)
      }
    }
    //value为编辑框输入的内容，这里我监听了一下值，当父组件调用得时候，如果给value赋值了，子组件将会显示父组件赋给的值
  },
  mounted() {
    this.seteditor()
    this.editor.txt.html(this.value)
  },
  methods: {
    seteditor() {
      // http://192.168.2.125:8080/admin/storage/create
      this.editor = new E(this.$refs.toolbar, this.$refs.editor)
      this.editor.customConfig.uploadImgShowBase64 = false // base 64 存储图片
      this.editor.customConfig.uploadImgServer = this.$baseurl.server + '/file/upload'// 配置服务器端地址
      this.editor.customConfig.uploadImgHeaders = { token: localStorage.getItem("xxxx_admin_token") }// 自定义 header
      this.editor.customConfig.uploadFileName = 'file' // 后端接受上传文件的参数名
      this.editor.customConfig.uploadImgMaxSize = 2 * 1024 * 1024 // 将图片大小限制为 2M
      this.editor.customConfig.uploadImgMaxLength = 1 // 限制一次最多上传 3 张图片
      this.editor.customConfig.uploadImgTimeout = 3 * 60 * 1000 // 设置超时时间

      // 配置菜单
      this.editor.customConfig.menus = [
        'head', // 标题
        'bold', // 粗体
        'fontSize', // 字号
        'fontName', // 字体
        'italic', // 斜体
        'underline', // 下划线
        'strikeThrough', // 删除线
        'foreColor', // 文字颜色
        'backColor', // 背景颜色
        'link', // 插入链接
        'list', // 列表
        'justify', // 对齐方式
        'quote', // 引用
        'emoticon', // 表情
        'image', // 插入图片
        'table', // 表格
        // 'video', // 插入视频
        // 'code', // 插入代码
        'undo', // 撤销
        'redo', // 重复
      ]

      // 自定义字体
      this.editor.customConfig.fontNames = [
        '微软雅黑',
        '宋体',
        '黑体',
        'Arial',
        'Tahoma',
        'Verdana'
      ]

      // 自定义配置颜色（字体颜色、背景色）
      this.editor.customConfig.colors = [
        '#000000',
        '#eeece0',
        '#1c487f',
        '#4d80bf',
        '#c24f4a',
        '#8baa4a',
        '#7b5ba1',
        '#46acc8',
        '#f9963b',
        '#ffffff',
        '#009ddc',
        '#E83344',
      ]

      //图片设置
      this.editor.customConfig.uploadImgHooks = {
        fail: (xhr, editor, result) => {
          // 插入图片失败回调
          // console.log(xhr);
        },
        success: (xhr, editor, result) => {
          // 图片上传成功回调
          // console.log(xhr);
        },
        timeout: (xhr, editor) => {
          // 网络超时的回调
          // console.log(xhr);
        },
        error: (xhr, editor) => {
          // 图片上传错误的回调
          // console.log(xhr);
        },
        customInsert: (insertImg, result, editor) => {
          insertImg(result.data)
        }
      }

      this.editor.customConfig.onchange = (html) => {
        this.info_ = html // 绑定当前逐渐地值
        this.$emit('change', this.info_) // 将内容同步到父组件中
      }

      // 创建富文本编辑器
      this.editor.create()
    }
  }
}
</script>

<style lang="less">
.editor {
  width: 100%;
  margin: 0 auto;
  position: relative;
  z-index: 0;
  .toolbar {
    border: 1px solid #ccc;
    border-bottom: 0px;
  }
  .text {
    border: 1px solid #ccc;
    height: 500px;
  }
  .w-e-text {
    overflow-y: scroll;
  }
}
</style>