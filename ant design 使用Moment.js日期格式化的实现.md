

# ant design 使用Moment.js日期格式化的实现

由于用ant design日期组件取得的值是moment类型，而往数据库中保存需要的是字符串类型.这里就用到了moment日期转换

Moment.js官网:http://momentjs.cn/

### 转换示例

#### 日期格式化

```js
moment().format('MMMM Do YYYY, h:mm:ss a'); // 十月 22日 2021, 3:05:48 下午
moment().format('dddd');                    // 星期五
moment().format("MMM Do YY");               // 10月 22日 21
moment().format('YYYY [escaped] YYYY');     // 2021 escaped 2021
moment().format();                          // 2021-10-22T15:05:48+08:00
```

#### 相对时间

```js
moment("20111031", "YYYYMMDD").fromNow(); // 10 年前
moment("20120620", "YYYYMMDD").fromNow(); // 9 年前
moment().startOf('day').fromNow();        // 15 小时前
moment().endOf('day').fromNow();          // 9 小时内
moment().startOf('hour').fromNow();       // 6 分钟前
```

#### 日历时间

```js
moment().subtract(10, 'days').calendar(); // 2021/10/12
moment().subtract(6, 'days').calendar();  // 上星期六15:05
moment().subtract(3, 'days').calendar();  // 上星期二15:05
moment().subtract(1, 'days').calendar();  // 昨天15:05
moment().calendar();                      // 今天15:05
moment().add(1, 'days').calendar();       // 明天15:05
moment().add(3, 'days').calendar();       // 下星期一15:05
moment().add(10, 'days').calendar();      // 2021/11/01
```

#### 多语言支持

```js
moment.locale();         // zh-cn
moment().format('LT');   // 15:05
moment().format('LTS');  // 15:05:48
moment().format('L');    // 2021/10/22
moment().format('l');    // 2021/10/22
moment().format('LL');   // 2021年10月22日
moment().format('ll');   // 2021年10月22日
moment().format('LLL');  // 2021年10月22日下午3点05分
moment().format('lll');  // 2021年10月22日 15:05
moment().format('LLLL'); // 2021年10月22日星期五下午3点05分
moment().format('llll'); // 2021年10月22日星期五 15:05
```

### ant design转换



```js
import moment from 'moment'
async function GetFySortTitle() {
  
  await article.GetFyTitleAsync(1, 1000, true, false).then((result: any) => {
  //遍历日期
    result.data.forEach((res: any) => {
      res.timeCreate = moment(res.timeCreate).format('YYYY-MM-DD')
      res.timeModified = moment(res.timeModified).format('YYYY-MM-DD')
    })
    state.dataResult = result.data
  })
}
```

### 函数封装

```js
//封装momentData
function momentData(result: any) {
  result.data.forEach((res: any) => {
    res.timeCreate = moment(res.timeCreate).format('YYYY-MM-DD')
    res.timeModified = moment(res.timeModified).format('YYYY-MM-DD')
  })
}

//调用
momentData(result)
```

