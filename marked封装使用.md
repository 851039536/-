# marked封装使用

### 封装markdown.js

```js
/*
 * @Author: your name
 * @Date: 2021-05-04 16:07:50
 * @LastEditTime: 2021-05-04 16:29:16
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\utils\markdown.js
 */

// eslint-disable-next-line @typescript-eslint/no-var-requires
const highlight = require("highlight.js");
// eslint-disable-next-line @typescript-eslint/no-var-requires
const marked = require("marked");
const tocObj = {
  add: function (text, level) {
    var anchor = `#toc${level}${++this.index}`;
    this.toc.push({
      anchor: anchor,
      level: level,
      text: text
    });
    return anchor;
  },
  // 使用堆栈的方式处理嵌套的ul,li，level即ul的嵌套层次，1是最外层
  // <ul>
  //   <li></li>
  //   <ul>
  //     <li></li>
  //   </ul>
  //   <li></li>
  // </ul>
  toHTML: function () {
    let levelStack = [];
    let result = "";
    const addStartUL = () => {
      result += '<ul class="anchor-ul" id="anchor-fix">';
    };
    const addEndUL = () => {
      result += "</ul>\n";
    };
    const addLI = (anchor, text) => {
      result +=
        '<li><a class="toc-link" href="#' + anchor + '">' + text + "<a></li>\n";
    };

    this.toc.forEach(function (item) {
      let levelIndex = levelStack.indexOf(item.level);
      // 没有找到相应level的ul标签，则将li放入新增的ul中
      if (levelIndex === -1) {
        levelStack.unshift(item.level);
        addStartUL();
        addLI(item.anchor, item.text);
      } // 找到了相应level的ul标签，并且在栈顶的位置则直接将li放在此ul下
      else if (levelIndex === 0) {
        addLI(item.anchor, item.text);
      } // 找到了相应level的ul标签，但是不在栈顶位置，需要将之前的所有level出栈并且打上闭合标签，最后新增li
      else {
        while (levelIndex--) {
          levelStack.shift();
          addEndUL();
        }
        addLI(item.anchor, item.text);
      }
    });
    // 如果栈中还有level，全部出栈打上闭合标签
    while (levelStack.length) {
      levelStack.shift();
      addEndUL();
    }
    // 清理先前数据供下次使用
    this.toc = [];
    this.index = 0;
    return result;
  },
  toc: [],
  index: 0
};

class MarkUtils {
  constructor() {
    this.rendererMD = new marked.Renderer();
    this.rendererMD.heading = function (text, level) {
      var anchor = tocObj.add(text, level);
      return `<h${level} id=${anchor}>${text}</h${level}>\n`;
    };
    highlight.configure({
      useBR: true
    });
    marked.setOptions({
      renderer: this.rendererMD,
      headerIds: false,
      gfm: true,
      tables: true,
      breaks: false,
      pedantic: false,
      sanitize: false,
      smartLists: true,
      smartypants: false,
      highlight: function (code) {
        return highlight.highlightAuto(code).value;
      }
    });
  }

  async marked(data) {
    if (data) {
      let content = await marked(data); // 文章内容
      let toc = tocObj.toHTML(); // 文章目录
      return {
        content: content,
        toc: toc
      };
    } else {
      return null;
    }
  }
}

const markdown = new MarkUtils();

export default markdown;
```

### 导入



```vue
 import markdown from "@/utils/markdown.js";
```

### 使用

```js
// 使用 marked 转换
              const article = markdown.marked(state.article_String.text);
              article.then((response: any) => {
                state.blog = response.content;

              });
```

