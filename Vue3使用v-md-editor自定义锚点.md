### Vue3使用v-md-editor自定义锚点

预览模式展示了如何在预览模式下通过外部锚点来定位到文档标题位置。



### 官网示例

https://ckang1229.gitee.io/vue-markdown-editor/zh/senior/anchor.html

### xxx.vue

```vue
<template>
  <div class="anchor">
    <div class="anchor_tag" v-for="anchor in state.titles" :key="anchor" @click="handleAnchorClick(anchor)">
      {{ anchor.title }}
    </div>
  </div>
  <div class="vmd">
    <v-md-preview :text="state.blog.text" ref="preview" />
  </div>
</template>
```



###  script setup 

```js
<script setup lang="ts">
import { useRoute } from 'vue-router'
import { reactive, onMounted, ref, getCurrentInstance } from 'vue'
import { article } from '@/api/index'

const route = useRoute()
interface State {
  blog: any
  id: any
  titles: any
}
const state: State = reactive({
  blog: [],
  id: route.query.id,
  titles: []
})

const GetAll = async () => {
  await article.GetByIdAsync(state.id, true).then((res: any) => {
    state.blog = res.data[0]
  })
}

const { proxy } = getCurrentInstance()
const preview = ref()
function handleAnchorClick(anchor: any) {
  const { lineIndex } = anchor
  const heading = preview.value.$el.querySelector(`[data-v-md-line="${lineIndex}"]`)
  if (heading) {
    preview.value.scrollToTarget({
      target: heading,
      scrollContainer: window,
      top: 60
    })
  }
}

onMounted(async () => {
  console.log('[ proxy ]', proxy.$refs.preview.$el)
  await GetAll()
  const anchors = proxy.$refs.preview.$el.querySelectorAll('h1,h2,h3,h4,h5,h6')
  const titles = Array.from(anchors).filter(title => !!title.innerText.trim())
  if (!titles.length) {
    state.titles = []
    return
  }
  const hTags = Array.from(new Set(titles.map(title => title.tagName))).sort()
  state.titles = titles.map(el => ({
    title: el.innerText,
    lineIndex: el.getAttribute('data-v-md-line'),
    indent: hTags.indexOf(el.tagName)
  }))
})
</script>
```

