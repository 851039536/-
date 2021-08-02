# vue富文本vue-quill-editor



### 1.安装

```markdown
npm install vue-quill-editor -S
```

#### 2.组件挂载

#### 全局挂载

```js
// main.js
import Vue from 'vue'
import VueQuillEditor from 'vue-quill-editor'
import 'quill/dist/quill.core.css' // import styles
import 'quill/dist/quill.snow.css' // for snow theme
import 'quill/dist/quill.bubble.css' // for bubble theme
Vue.use(VueQuillEditor, /* { default global options } */)

```

#### 局部挂载

```js
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import { quillEditor } from "vue-quill-editor";

components: {
    quillEditor
  },
```

### 2.组件使用

```vue
                  <quill-editor
                    ref="myQuillEditor"
                    v-model="form.talkText"
                    :options="editorOption"
                    @blur="onEditorBlur($event)"
                    @focus="onEditorFocus($event)"
                    @ready="onEditorReady($event)"
                    class="editor"
                  />
```

### 3.配置

```js
 //富文本编辑器配置
      editorOption: {
        //  富文本编辑器配置
        modules: {
          //工具栏定义的
          toolbar: toolbarOptions
        },
        //主题
        theme: "snow",
        placeholder: "请输入正文"
      },
          
    methods: {
      //失去焦点事件
      onEditorBlur(quill) {
        console.log('editor blur!', quill)
      },
      //获得焦点事件
      onEditorFocus(quill) {
        console.log('editor focus!', quill)
      },
      // 准备富文本编辑器
      onEditorReady(quill) {
        console.log('editor ready!', quill)
      },
      //内容改变事件
      onEditorChange({ quill, html, text }) {
        console.log('editor change!', quill, html, text)
        this.content = html
      }
    }
          
```

#### **工具栏**

```js
// 工具栏配置
const toolbarOptions = [
  ["bold", "italic", "underline", "strike"], // 加粗 斜体 下划线 删除线 -----['bold', 'italic', 'underline', 'strike']
  ["blockquote", "code-block"], // 引用  代码块-----['blockquote', 'code-block']
  [
    {
      header: 2
    },
    {
      header: 3
    }
  ], // 1、2 级标题-----[{ header: 1 }, { header: 2 }]
  [
    {
      list: "ordered"
    },
    {
      list: "bullet"
    }
  ], // 有序、无序列表-----[{ list: 'ordered' }, { list: 'bullet' }]
  [
    {
      script: "sub"
    },
    {
      script: "super"
    }
  ], // 上标/下标-----[{ script: 'sub' }, { script: 'super' }]
  [
    {
      indent: "-1"
    },
    {
      indent: "+1"
    }
  ], // 缩进-----[{ indent: '-1' }, { indent: '+1' }]
  [
    {
      direction: "rtl"
    }
  ], // 文本方向-----[{'direction': 'rtl'}]
  [
    {
      size: ["small", false, "large", "huge"]
    }
  ], // 字体大小-----[{ size: ['small', false, 'large', 'huge'] }]
  [
    {
      header: [1, 2, 3, 4, 5, 6, false]
    }
  ], // 标题-----[{ header: [1, 2, 3, 4, 5, 6, false] }]
  [
    {
      color: []
    },
    {
      background: []
    }
  ], // 字体颜色、字体背景颜色-----[{ color: [] }, { background: [] }]
  [
    {
      font: []
    }
  ], // 字体种类-----[{ font: [] }]
  [
    {
      align: []
    }
  ], // 对齐方式-----[{ align: [] }]
  ["clean"], // 清除文本格式-----['clean']
  ["image", "video"] // 链接、图片、视频-----['link', 'image', 'video']
];
```

#### css

```css
.editor {
  line-height: normal !important;
  height: 500px;
}

.ql-snow .ql-tooltip[data-mode="link"]::before {
  content: "请输入链接地址:";
}

.ql-snow .ql-tooltip.ql-editing a.ql-action::after {
  border-right: 0px;
  content: "保存";
  padding-right: 0px;
}

.ql-snow .ql-tooltip[data-mode="video"]::before {
  content: "请输入视频地址:";
}

.ql-snow .ql-picker.ql-size .ql-picker-label::before,
.ql-snow .ql-picker.ql-size .ql-picker-item::before {
  content: "14px";
}

.ql-snow .ql-picker.ql-size .ql-picker-label[data-value="small"]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value="small"]::before {
  content: "10px";
}

.ql-snow .ql-picker.ql-size .ql-picker-label[data-value="large"]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value="large"]::before {
  content: "18px";
}

.ql-snow .ql-picker.ql-size .ql-picker-label[data-value="huge"]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value="huge"]::before {
  content: "32px";
}

.ql-snow .ql-picker.ql-header .ql-picker-label::before,
.ql-snow .ql-picker.ql-header .ql-picker-item::before {
  content: "文本";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="1"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="1"]::before {
  content: "标题1";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="2"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="2"]::before {
  content: "标题2";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="3"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="3"]::before {
  content: "标题3";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="4"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="4"]::before {
  content: "标题4";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="5"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="5"]::before {
  content: "标题5";
}

.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="6"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="6"]::before {
  content: "标题6";
}

.ql-snow .ql-picker.ql-font .ql-picker-label::before,
.ql-snow .ql-picker.ql-font .ql-picker-item::before {
  content: "标准字体";
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="serif"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="serif"]::before {
  content: "衬线字体";
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="monospace"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="monospace"]::before {
  content: "等宽字体";
}
```

