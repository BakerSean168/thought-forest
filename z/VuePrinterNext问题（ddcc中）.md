---
tags:
  - tech/dev/frame
  - type/howto
  - status/growing
description: VuePrinterNext问题（ddcc中）
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[前端基础 MOC]]

---


## 测试demo 代码

### main.ts

导入 printPlugin

```ts
// 引入unocss css
import '@/plugins/unocss'

// 导入全局的svg图标
import '@/plugins/svgIcon'

// 初始化多语言
import { setupI18n } from '@/plugins/vueI18n'

// 引入状态管理
import { setupStore } from '@/store'

// 全局组件
import { setupGlobCom } from '@/components'

// 引入 element-plus
import { setupElementPlus } from '@/plugins/elementPlus'

// 引入 form-create
import { setupFormCreate } from '@/plugins/formCreate'

// 引入全局样式
import '@/styles/index.scss'

// 引入动画
import '@/plugins/animate.css'

// 路由
import router, { setupRouter } from '@/router'

// 权限
import { setupAuth } from '@/directives'
import { printPlugin } from 'vue-print-next'  // 导入 printPlugin!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

import { createApp } from 'vue'

import App from './App.vue'

import './permission'

import '@/plugins/tongji' // 百度统计
import Logger from '@/utils/Logger'

import VueDOMPurifyHTML from 'vue-dompurify-html' // 解决v-html 的安全隐患

import emitter from '@/utils/emitter'
// 创建实例
const setupAll = async () => {
  const app = createApp(App)
  // 去除警告
  app.config.warnHandler = () => null
  await setupI18n(app)

  setupStore(app)

  setupGlobCom(app)

  setupElementPlus(app)

  setupFormCreate(app)

  setupRouter(app)

  setupAuth(app)

  await router.isReady()

  app.use(VueDOMPurifyHTML).use(printPlugin)  // 导入 printPlugin!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

  app.mount('#app')
}

setupAll()

Logger.prettyPrimary(`Welcome to use`, import.meta.env.VITE_APP_TITLE)

```

### PrinterAdvance.vue

在 ddcc 中随便覆盖一个页面用于测试

```vue
<script setup lang="ts">
import { ref, type Ref } from 'vue';
import type { Orientation, PaperSize } from 'vue-print-next';
import { VuePrintNext } from 'vue-print-next';

// --- PrintPageLayout Props (从第一个文件) ---
// 统一布局组件，用于包装所有打印示例页面
defineProps({
  // 页面标题
  title: {
    type: String,
    required: true
  },
  // 页面描述
  description: {
    type: String,
    default: ''
  },
  // 是否显示打印选项卡片
  showOptions: {
    type: Boolean,
    default: true
  }
});

/*
 * @Author: zi.yang
 * @Date: 2025-05-09 19:48:03
 * @LastEditors: zi.yang
 * @LastEditTime: 2025-05-09 19:48:25
 * @Description:
 * @FilePath: /vue-print-next/demos/vue3-demo/src/utils/common.ts
 */
// 可用的纸张尺寸
const paperSizes = [
  { value: 'A0', label: 'A0 (841mm × 1189mm)' },
  { value: 'A1', label: 'A1 (594mm × 841mm)' },
  { value: 'A2', label: 'A2 (420mm × 594mm)' },
  { value: 'A3', label: 'A3 (297mm × 420mm)' },
  { value: 'A4', label: 'A4 (210mm × 297mm)' },
  { value: 'A5', label: 'A5 (148mm × 210mm)' },
  { value: 'A6', label: 'A6 (105mm × 148mm)' },
  { value: 'A7', label: 'A7 (74mm × 105mm)' },
  { value: 'A8', label: 'A8 (52mm × 74mm)' },
  { value: 'Letter', label: 'Letter (215.9mm × 279.4mm)' },
  { value: 'Legal', label: 'Legal (215.9mm × 355.6mm)' },
  { value: 'Tabloid', label: 'Tabloid (279.4mm × 431.8mm)' },
  { value: 'custom', label: '自定义尺寸' },
];

// --- 打印逻辑状态 (从第二个文件) ---
// 当前选择的纸张尺寸
const selectedPaperSize: Ref<PaperSize> = ref('A4');
// 纸张方向
const orientation: Ref<Orientation> = ref('portrait');
// 自定义尺寸
const customWidth = ref(210);
const customHeight = ref(297);
const customUnit = ref('mm');
// 是否启用深色模式
const darkMode = ref(false);
// 是否启用窗口模式
const windowMode = ref(false);
// 缩放比例
const scale = ref(1);

// 打印不同的部分
function printSection(sectionId: string, title: string) {
  new VuePrintNext({
    el: `#${sectionId}`,
    preview: true,
    paperSize: 'A4',
    orientation: 'portrait',
    darkMode: darkMode.value,
    previewTitle: title,
  });
}

// 组合打印功能
function printCombined() {
  const printOptions: any = {
    el: '#section1, #section2, #section3',
    preview: true,
    paperSize: selectedPaperSize.value,
    orientation: orientation.value,
    darkMode: darkMode.value,
    windowMode: windowMode.value,
    defaultScale: scale.value,
    previewTitle: '组合打印示例',
  };

  if (selectedPaperSize.value === 'custom') {
    Object.assign(printOptions, {
      customSize: {
        width: customWidth.value + '',
        height: customHeight.value + '',
        unit: customUnit.value,
      },
    });
  }

  new VuePrintNext(printOptions);
}
</script>

<template>
  <div class="print-container fade-in">
    <div class="header-section">
      <h2 class="page-title">VuePrintNext 高级功能示例</h2>
      <p v-if="description" class="page-description">
        本示例展示了vue-print-next的高级打印功能，包括自定义纸张尺寸、方向和缩放选项
      </p>
    </div>

    <div class="card-container">
      <div v-if="true" class="print-options-card">
        <div class="card-header text-center">
          <span class="card-icon">⚙️</span>
          <h3>打印选项</h3>
        </div>
        <div class="card-content">
          <div class="settings-panel">
            <div class="setting-group">
              <label>纸张尺寸：</label>
              <select v-model="selectedPaperSize" class="select-input">
                <option
                  v-for="size in paperSizes"
                  :key="size.value"
                  :value="size.value"
                >
                  {{ size.label }}
                </option>
              </select>
            </div>

            <div class="setting-group" v-if="selectedPaperSize === 'custom'">
              <div class="custom-size-inputs">
                <div>
                  <label>宽度：</label>
                  <input
                    type="number"
                    v-model="customWidth"
                    class="number-input"
                    min="1"
                  />
                </div>
                <div>
                  <label>高度：</label>
                  <input
                    type="number"
                    v-model="customHeight"
                    class="number-input"
                    min="1"
                  />
                </div>
                <div>
                  <label>单位：</label>
                  <select v-model="customUnit" class="select-input">
                    <option value="mm">毫米 (mm)</option>
                    <option value="cm">厘米 (cm)</option>
                    <option value="in">英寸 (in)</option>
                    <option value="pt">点 (pt)</option>
                  </select>
                </div>
              </div>
            </div>

            <div class="setting-group">
              <label>纸张方向：</label>
              <div class="radio-group">
                <label>
                  <input type="radio" v-model="orientation" value="portrait" />
                  纵向
                </label>
                <label>
                  <input type="radio" v-model="orientation" value="landscape" />
                  横向
                </label>
              </div>
            </div>

            <div class="setting-group">
              <label>显示选项：</label>
              <div class="checkbox-group">
                <label>
                  <input type="checkbox" v-model="darkMode" />
                  深色模式
                </label>
                <label>
                  <input type="checkbox" v-model="windowMode" />
                  窗口模式
                </label>
              </div>
            </div>

            <div class="setting-group">
              <label>缩放比例：{{ scale }}</label>
              <input
                type="range"
                @change="() => console.log(scale)"
                v-model="scale"
                min="0.5"
                max="2"
                step="0.1"
                class="range-input"
              />
            </div>
          </div>
          <div class="help-text">
            <i class="tip-icon">✨</i> 提示：点击下方按钮可以尝试不同的打印方式。
          </div>

          <div class="buttons-group">
            <button @click="printCombined" class="print-btn secondary">
              打印全部内容
            </button>

            <button
              @click="printSection('section1', '第一部分')"
              class="print-btn accent"
            >
              打印第一部分
            </button>
            <button
              @click="printSection('section2', '第二部分')"
              class="print-btn accent"
            >
              打印第二部分
            </button>
            <button
              @click="printSection('section3', '第三部分')"
              class="print-btn accent"
            >
              打印第三部分
            </button>
          </div>
        </div>
      </div>

      <div class="print-content">
        <div id="section1" class="print-section">
          <h3>第一部分：VuePrintNext 高级功能</h3>
          <div class="feature-card">
            <h4>自定义纸张尺寸</h4>
            <p>
              VuePrintNext 支持多种标准纸张尺寸，包括 A 系列、Letter、Legal 和
              Tabloid。
            </p>
            <p>
              您还可以通过 <code>paperSize: 'custom'</code> 和
              <code>customSize</code> 属性设置自定义纸张尺寸。
            </p>
            <div class="code-example">
              <pre>
{
  paperSize: 'custom',
  customSize: {
    width: 210,
    height: 297,
    unit: 'mm'
  }
}</pre
              >
            </div>
          </div>
        </div>

        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>
        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>
        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>
        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>
        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>
        <div id="section2" class="print-section">
          <h3>第二部分：显示选项</h3>
          <div class="feature-card">
            <h4>深色模式与窗口模式</h4>
            <p>
              VuePrintNext 支持深色模式打印预览，通过设置
              <code>darkMode: true</code> 启用。
            </p>
            <p>
              窗口模式允许在新窗口中打印预览，通过设置
              <code>windowMode: true</code> 启用。
            </p>
            <div class="preview-example" :class="{ dark: darkMode }">
              <div class="preview-content">
                <h5>预览示例</h5>
                <p>这是一个{{ darkMode ? '深色' : '浅色' }}模式的预览示例</p>
              </div>
            </div>
          </div>
        </div>

        <div id="section3" class="print-section">
          <h3>第三部分：缩放</h3>
          <div class="feature-card">
            <h4>缩放控制</h4>
            <p>
              通过
              <code>defaultScale</code> 属性可以设置打印预览的初始缩放比例。
            </p>
            <p>当前缩放比例：{{ scale }}</p>

            <h4>组合打印</h4>
            <p>
              VuePrintNext 支持打印多个元素，只需将多个选择器传递给
              <code>el</code> 属性。
            </p>
            <div class="code-example">
              <pre>
{
  el: '#section1, #section2, #section3',
  preview: true
}</pre
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* --- PrintPageLayout Styles (从第一个文件) --- */
/* 组件特定样式，其他样式由全局CSS提供 */
.header-section {
  margin-bottom: var(--spacing-lg);
  text-align: left;
}

/* 确保打印内容区域样式一致 */
.print-content {
  background-color: var(--bg-white);
  border-radius: var(--border-radius-md);
  padding: var(--spacing-lg);
  box-shadow: var(--shadow-sm);
  transition: var(--transition-normal);
  width: 100%;
}

/* 打印选项卡片样式 */
.print-options-card {
  width: 350px;
  flex-shrink: 0;
  background-color: var(--bg-light);
  border-radius: var(--border-radius-md);
  padding: var(--spacing-md);
  box-shadow: var(--shadow-sm);
  margin-right: var(--spacing-md);
}

@media (max-width: 768px) {
  .card-container {
    flex-direction: column;
  }

  .print-options-card {
    width: 100%;
    margin-right: 0;
    margin-bottom: var(--spacing-lg);
  }
}

/* 打印媒体查询 - 确保在打印时隐藏选项卡片 */
@media print {
  .print-options-card {
    display: none !important;
  }

  .print-content {
    box-shadow: none !important;
    padding: 0 !important;
  }
}

/* --- 第二个组件的 Styles --- */
/* 使用共享样式，只保留特定于此组件的样式 */
.section-buttons {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.buttons-group {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

/* 响应式设计增强 */
@media (max-width: 768px) {
  .section-buttons {
    flex-direction: column;
  }

  .buttons-group {
    flex-direction: column;
  }
}

/* 补充样式以确保预览示例正常显示，因为它依赖于 `darkMode` 状态 */
.preview-example {
  border: 1px solid #ccc;
  padding: 15px;
  margin-top: 10px;
  border-radius: 4px;
  background-color: #f9f9f9;
  color: #333;
}

.preview-example.dark {
  background-color: #333;
  color: #f0f0f0;
  border-color: #555;
}

.code-example pre {
  background-color: #eee;
  padding: 10px;
  border-radius: 4px;
  overflow-x: auto;
}

.preview-example.dark .code-example pre {
  background-color: #222;
}

.setting-group {
  margin-bottom: 10px;
}

.custom-size-inputs {
  display: flex;
  gap: 10px;
  align-items: center;
}
</style>
```

### 修补代码

在 App.vue 中添加样式

```vue
// 打印媒体样式，确保 打印时 不会受到 全局样式 影响，成功解决 打印 页面只显示一页的问题
@media print {
  html,
  body {
    overflow: visible !important;
    height: auto !important;
  }
}

// ai 添加的为了让 预览页面也能正确全部显示的代码，但是没用
/* vue-print-next preview: allow scrolling inside the page iframe */
#vue-print-next-previewBox .paperContainer > div {
  height: 100% !important;
  min-height: 100% !important;
  overflow: auto !important;
}


```


## 问题

### 预览页单页

预览页面仍然 只能显示单页

**查阅文档**：

1. 打印预览窗口无法正确加载？
	Q: 打印预览窗口无法正确加载或显示不全？
	A: 确保传入的 extraCss 和 extraHead 参数中的路径和内容正确。如果预览窗口仍然无法加载，请检查网络请求是否成功，或尝试在本地调试和修复问题。
2. 打印时出现样式问题？
	Q: 打印出来的内容样式与预期不符，可能是什么原因？
	A: 样式问题可能由以下几种原因造成：
		公共样式影响：
		例如 body 设置为 flex 会影响打印内容的宽度。请确保打印内容的容器具有固定宽度。
		overflow: hidden：如果 html 或 body 设置为 hidden，可能会导致打印时无法完全展示内容。
		font-size: 0, opacity: 0： 如果全局样式中存在以上类似的样式，可能会导致打印时问题、元素展示不出来。
		CSS 动画：如果页面上有 CSS 动画，可能会导致打印内容与实际展示不符。使用 preview: true 打开预览窗口，并等待动画完成后再进行打印。
3. 如何调试打印功能？
	Q: 在使用 VuePrintNext 时遇到调试困难，如何有效调试？
	A: 使用浏览器的开发者工具检查打印内容的 HTML 和 CSS。通过 console.log 输出调试信息，查看回调函数是否正常触发。确保使用的参数正确并且符合预期。


传入 extraHead

```ts
const previewExtraHead = `
  <style>
    html, body {
      overflow: auto !important;
      height: auto !important;
      min-height: 100% !important;
      width: auto !important;
      display: block !important;
    }
  </style>
`

function printSection(sectionId: string, title: string) {
  new VuePrintNext({
    el: `#${sectionId}`,
    preview: true,
    paperSize: 'A4',
    orientation: 'portrait',
    darkMode: darkMode.value,
    previewTitle: title,
    extraHead: previewExtraHead
  });
}
```

### 想通过 按钮 + 静默生成 + 直接展示 vuePrintNext 的预览页面

结果 预览页空白

他会携带原有样式，静默生成往往将样式设为 display;none，需要在 previewExtraHead 覆盖。

### 初步（没有pagedjs）

```
<PrintButton
  :module="3"
  :mode="2"
  type="success"
  plain
  :disabled="printList.length == 0"
/>
        
        
import PrintButton from '@/components/Print/components/PrintButton.vue'

const printList = ref<ArchiveRetrievalVO[]>([])

const openPrint = async (id: number) => {
  router.push({
    name: 'printDetail',
    query: { id, module: 3, type: 1 }
  })
  
/** 导出按钮操作 */
const handleExport = async () => {
  try {
    // 导出的二次确认
    await message.exportConfirm()
    // 发起导出
    exportLoading.value = true
    let data
    if (printList.value.length > 0) {
      const retrievalIdList = printList.value.map((item) => item.id)
      data = await ArchiveRetrievalApi.exportArchiveRetrieval({ retrievalIdList })
    } else {
      data = await ArchiveRetrievalApi.exportArchiveRetrieval(queryParams)
    }
    download.excel(data, 'retrieval.xls')
  } catch {
  } finally {
    exportLoading.value = false
  }
}
```

![[Pasted image 20251129181201.png]]


### 使用pagedjs

![[Pasted image 20251129204958.png]]

有滚动条


## 流程

vue-print-next 是打印组件
- 用于 preview 和 打印
- 需要将 要打印的元素的 id 传入 类，并且传入的 元素要可见（他会携带原本的css样式，可以通过 extraHead 来添加额外的样式来在 preview 和打印时 恢复可见性，以及添加额外属性）
- 它会把该 DOM **克隆到 iframe** 里同时复制样式（css text）

pagedjs 是渲染组件
- pagedjs 不会影响原始 dom，会动态创建一个新的dom（`.pagedjs_page` 包裹层）
- pagedjs 的渲染使用是要把 内容，样式，body 都传入，而非把 样式 嵌入 html

```ts
import { Previewer } from 'pagedjs';

let paged = new Previewer();
let flow = paged.preview(DOMContent, ["path/to/css/file.css"], document.body).then((flow) => {
	console.log("Rendered", flow.total, "pages.");
})
```

所以总的流程应该是：
1. 根据数据获取原始的 内容
2. 然后用 pagedjs 渲染，添加 @page 等用于实现 签名表底部重复渲染 + 其他内容自动分页 的分页PDF
3. 然后把 pagedjs 创建的 新 dom 传入 vue-print-next

问题是：
获取原始内容的函数中是不是不需要 @page 等分页样式，这些样式是在 pagedjs 渲染中添加吗，还有怎么调用 pagedjs 渲染
上面的流程是正确的流程嘛，请你分析，并给出最佳方式，并帮我重新实现一下，现在的实现有问题，可以直接大重构
