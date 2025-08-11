---
title: vuex与pinia
date: 2025-08-11 09:33:44
tags: vue状态库pinia、vuex
categories: 前端
---

Vuex 和 Pinia 都是 Vue 生态中用于状态管理的库，其中 Pinia 是 Vuex 的继任者（官方推荐使用 Pinia）。它们的核心目标相同，但在 API 设计、功能特性和使用体验上有显著区别：

### 1. 核心结构差异
- **Vuex**：采用复杂的嵌套结构，包含 `state`、`mutations`、`actions`、`getters`、`modules` 等概念，其中 `mutations` 是同步更新状态的唯一方式，`actions` 处理异步逻辑。
  ```javascript
  // Vuex 示例
  const store = new Vuex.Store({
    state: { count: 0 },
    mutations: {
      increment(state) { state.count++ } // 必须同步
    },
    actions: {
      asyncIncrement({ commit }) {
        setTimeout(() => commit('increment'), 1000)
      }
    }
  })
  ```

- **Pinia**：简化了结构，取消了 `mutations`，仅保留 `state`、`actions`、`getters`，且 `actions` 支持同步和异步操作，更接近原生 JavaScript 逻辑。
  ```javascript
  // Pinia 示例
  const useCounterStore = defineStore('counter', {
    state: () => ({ count: 0 }),
    actions: {
      increment() { this.count++ }, // 同步
      async asyncIncrement() {
        await new Promise(resolve => setTimeout(resolve, 1000))
        this.count++
      }
    }
  })
  ```

### 2. 模块化管理
- **Vuex**：需要通过 `modules` 手动拆分模块，且模块内部可能需要 `namespaced: true` 来避免命名冲突，嵌套模块的访问和调用较为繁琐。
  
- **Pinia**：没有 `modules` 概念，每个 `defineStore` 调用即创建一个独立模块（store），天然支持模块化，且无需手动处理命名空间，使用更直观。

### 3. 类型支持
- **Vuex**：对 TypeScript 的支持不够友好，需要手动定义大量类型，类型推断能力较弱。
  
- **Pinia**：专为 TypeScript 设计，提供完善的类型推断，无需额外类型声明即可获得良好的类型提示，开发体验更优。

### 4. 状态修改方式
- **Vuex**：必须通过 `mutations` 修改状态（遵循"单向数据流"规范），写法相对冗余。
  
- **Pinia**：可以直接修改状态（`store.count++`），也可在 `actions` 中修改，更灵活，同时保留了状态追踪能力。

### 5. 插件兼容性
- **Vuex**：有自己的插件生态，但部分插件可能不兼容 Vue 3。
  
- **Pinia**：官方支持 Vue 2 和 Vue 3，且兼容多数 Vuex 插件的功能（如持久化存储），同时提供更简洁的插件 API。

### 6. 官方态度
- Vuex 4 是 Vue 3 的兼容版本，但已停止新功能开发。
- Pinia 是 Vue 官方推荐的状态管理库，被视为 Vuex 5 的替代方案，将长期维护。

### 总结
- 若项目使用 Vue 3 + TypeScript，优先选择 **Pinia**，体验更现代、简洁。
- 若维护旧项目或依赖 Vuex 特定功能，可继续使用 **Vuex**，但建议新项目迁移到 Pinia。

Pinia 本质上是对 Vuex 的简化和优化，保留了核心功能的同时，大幅降低了使用门槛，更符合 Vue 3 的 Composition API 风格。

## pinia使用教程

下面是一个详细的 Pinia 使用教程，涵盖从安装到高级用法的核心内容：

### 一、安装 Pinia
首先需要安装 Pinia 包：

```bash
# Vue 3 项目
npm install pinia
# 或
yarn add pinia
```

### 二、创建 Pinia 实例并挂载
在入口文件（如 `main.js`）中创建并挂载 Pinia：

```javascript
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

// 创建 Pinia 实例
const pinia = createPinia()
// 创建 Vue 应用
const app = createApp(App)

// 挂载 Pinia 到应用
app.use(pinia)
app.mount('#app')
```

### 三、定义 Store
使用 `defineStore()` 定义一个 Store（每个 Store 对应一个模块）：
```
import { defineStore } from 'pinia'

// 第一个参数是唯一 ID（用于 devtools）
export const useCounterStore = defineStore('counter', {
  // 状态（类似组件的 data）
  state: () => ({
    count: 0,
    user: {
      name: '张三',
      age: 20
    },
    list: []
  }),

  // 计算属性（类似组件的 computed）
  getters: {
    // 基础用法
    doubleCount: (state) => state.count * 2,
    
    // 使用其他 getter
    doubleCountPlusOne() {
      return this.doubleCount + 1
    },
    
    // 带参数的 getter（返回函数）
    getUserById: (state) => (id) => {
      // 实际应用中可能是从 state.list 中查找
      return state.user
    }
  },

  // 方法（类似组件的 methods，支持同步和异步）
  actions: {
    // 同步方法
    increment() {
      this.count++
    },
    
    // 带参数的方法
    add(amount) {
      this.count += amount
    },
    
    // 异步方法
    async fetchData() {
      try {
        // 模拟 API 请求
        const response = await new Promise(resolve => {
          setTimeout(() => resolve([1, 2, 3]), 1000)
        })
        this.list = response
      } catch (error) {
        console.error('获取数据失败', error)
      }
    }
  }
})

```



### 四、在组件中使用 Store
在 Vue 组件中引入并使用定义好的 Store：

```vue
<template>
  <div>
    <p>计数: {{ counterStore.count }}</p>
    <p>双倍计数: {{ counterStore.doubleCount }}</p>
    <button @click="counterStore.increment">+1</button>
    <button @click="counterStore.add(5)">+5</button>
    <button @click="counterStore.fetchData">加载数据</button>
    <ul>
      <li v-for="item in counterStore.list" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>

<script setup>
// 引入 Store
import { useCounterStore } from './stores/counter'

// 创建 Store 实例（不需要 .value，直接使用）
const counterStore = useCounterStore()
</script>
```

### 五、状态修改的多种方式
1. **直接修改**（简单场景推荐）：
```javascript
counterStore.count = 10
counterStore.user.name = '李四'
```

2. **使用 $patch 批量修改**（多个状态修改时推荐）：
```javascript
counterStore.$patch({
  count: 10,
  'user.name': '李四' // 嵌套属性修改
})

// 函数式修改（复杂逻辑）
counterStore.$patch((state) => {
  state.count += 10
  state.list.push(4)
})
```

3. **通过 actions 修改**（复杂逻辑或异步操作）：
```javascript
// 调用 actions 中定义的方法
counterStore.increment()
await counterStore.fetchData()
```

### 六、状态监听
使用 `$subscribe` 监听状态变化：

```javascript
// 在组件中
const counterStore = useCounterStore()

// 监听所有状态变化
counterStore.$subscribe((mutation, state) => {
  console.log('状态变化:', mutation, state)
  // 可用于持久化存储
  localStorage.setItem('counter', JSON.stringify(state))
})

// 监听特定状态（第二个参数是配置项）
counterStore.$subscribe(
  (mutation, state) => {
    console.log('count 变化:', state.count)
  },
  { predicate: (mutation) => mutation.storeId === 'counter' && mutation.type === 'count' }
)
```

### 七、Store 间交互
不同 Store 之间可以直接相互调用：

```javascript
// stores/user.js
import { defineStore } from 'pinia'
import { useCounterStore } from './counter'

export const useUserStore = defineStore('user', {
  actions: {
    logout() {
      // 调用其他 Store 的方法
      const counterStore = useCounterStore()
      counterStore.count = 0 // 重置计数器
      // 执行其他退出逻辑...
    }
  }
})
```

### 八、持久化存储（插件示例）
使用 `pinia-plugin-persistedstate` 实现状态持久化：

1. 安装插件：
```bash
npm install pinia-plugin-persistedstate
```

2. 配置插件：
```javascript
// main.js
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
```

3. 在 Store 中启用持久化：
```javascript
export const useCounterStore = defineStore('counter', {
  // ...其他配置
  persist: true, // 简单启用（默认存储到 localStorage）
  
  // 高级配置
  // persist: {
  //   key: 'my-counter', // 存储键名
  //   storage: sessionStorage, // 存储方式
  //   paths: ['count'] // 只持久化 count 字段
  // }
})
```

### 总结
Pinia 的核心优势在于：
- 简洁的 API（无 mutations）
- 完善的 TypeScript 支持
- 更灵活的状态修改方式
- 天然的模块化设计

通过上述步骤，你可以快速掌握 Pinia 的基本使用。对于复杂项目，建议按功能拆分不同的 Store，保持状态管理的清晰性。

