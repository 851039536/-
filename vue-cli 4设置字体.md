# vue-cli 4设置字体

#### 1 下载tff格式的字体

#### 2 assets中新建文件夹fonts，把tff字体文件放在fonts，并在assets/css中新建font.css

```css
@font-face {
    font-family: "Acy";
    src: url('../fonts/Acy.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
}
```

#### 3、在mian.js中引用font.css

```javascript
import './assets/css/font.css'
```

#### 4、在需要使用的地方设置新字体样式就可以了

```css
font-family: "Acy";
```