vuex4 的源码比较短小精悍，在此对其源码进行简单的分析：

## install

vuex 中创建 store 的方法是 createStore，而其中创建 store 则是通过 new Store 的方式

```js
export function createStore(options) {
  return new Store(options);
}
```

在 vuex4 中，vuex 的插件注册有了变化：

```js
Store.prototype.install = function install(app, injectKey) {
  app.provide(injectKey || storeKey, this);
  // 组件内部可以通过this.$store去访问
  app.config.globalProperties.$store = this;
  // ...
};
```

一种在 app 上添加了全局的$store 属性来访问 store，其次通过 provide、inject 来访问 store。
对应的 useStore：

```js
import { inject } from "vue";
// 默认store key
export const storeKey = "store";

export function useStore(key = null) {
  return inject(key !== null ? key : storeKey);
}
```

## Store

核心 Store 类的代码：

```js
// 只关心核心代码
export class Store {
  constructor(options = {}) {
    const {
      // 用户自定义插件
      plugins = [],
      // 严格模式
      // 在严格模式下，无论何时发生了状态变更且不是由 mutation 函数引起的，将会抛出错误。这能保证所有的状态变更都能被调试工具跟踪到。
      strict = false,
      // 打开-关闭devtools
      devtools,
    } = options;

    // store internal state
    this._committing = false;
    // 保存actions
    this._actions = Object.create(null);
    // actions 订阅
    this._actionSubscribers = [];
    // 保存mutations
    this._mutations = Object.create(null);
    // 保存Getter
    this._wrappedGetters = Object.create(null);
    // 模块化
    this._modules = new ModuleCollection(options);
    this._modulesNamespaceMap = Object.create(null);
    // 保存Store实例订阅
    this._subscribers = [];
    this._makeLocalGettersCache = Object.create(null);
    // 是否开启开发调试工具
    this._devtools = devtools;
    // this指向Store实例
    // Store实例绑定commit和dispatch方法，在options api const store = useStore()使用commit和dispatch方法
    const store = this;
    const { dispatch, commit } = this;
    this.dispatch = function boundDispatch(type, payload) {
      return dispatch.call(store, type, payload);
    };
    this.commit = function boundCommit(type, payload, options) {
      return commit.call(store, type, payload, options);
    };

    this.strict = strict;
    // 获取用户声明state
    const state = this._modules.root.state;

    // 注册配置module
    installModule(this, state, [], this._modules.root);

    // 初始化state
    resetStoreState(this, state);

    // 注册组件
    plugins.forEach((plugin) => plugin(this));
  }

  install(app, injectKey) {
    // provide 为 composition API 中使用
    //  可以传入 injectKey  如果没传取默认的 storeKey 也就是 store
    app.provide(injectKey || storeKey, this);
    // 为 option API 中使用
    app.config.globalProperties.$store = this;
  }
  // 类属性getter
  get state() {
    return this._state.data;
  }
  // 修改拦截
  set state(v) {
    if (__DEV__) {
      assert(
        false,
        `use store.replaceState() to explicit replace store state.`
      );
    }
  }

  commit(_type, _payload, _options) {
    // 检查commit调用参数方式，统一调用参数
    const { type, payload, options } = unifyObjectStyle(
      _type,
      _payload,
      _options
    );

    const mutation = { type, payload };
    // 依据commit提交参数type查找mutations配置对应处理方法
    const entry = this._mutations[type];
    if (!entry) {
      // 开发环境，为找到对应处理函数--提示
      if (__DEV__) {
        console.error(`[vuex] unknown mutation type: ${type}`);
      }
      return;
    }
    // 循环处理mutations绑定函数
    this._withCommit(() => {
      entry.forEach(function commitIterator(handler) {
        handler(payload);
      });
    });
    // 通知订阅
    this._subscribers
      .slice() // shallow copy to prevent iterator invalidation if subscriber synchronously calls unsubscribe
      .forEach((sub) => sub(mutation, this.state));

    if (__DEV__ && options && options.silent) {
      console.warn(
        `[vuex] mutation type: ${type}. Silent option has been removed. ` +
          "Use the filter functionality in the vue-devtools"
      );
    }
  }

  dispatch(_type, _payload) {
    // 检查dispatch调用参数方式并统一化
    const { type, payload } = unifyObjectStyle(_type, _payload);

    const action = { type, payload };
    // 依据action调用参数type获取actions声明处理函数
    const entry = this._actions[type];
    if (!entry) {
      // 开发环境-未找到对应处理函数-提示
      if (__DEV__) {
        console.error(`[vuex] unknown action type: ${type}`);
      }
      return;
    }
    // 处理订阅action
    try {
      this._actionSubscribers
        .slice() // shallow copy to prevent iterator invalidation if subscriber synchronously calls unsubscribe
        .filter((sub) => sub.before)
        .forEach((sub) => sub.before(action, this.state));
    } catch (e) {
      if (__DEV__) {
        console.warn(`[vuex] error in before action subscribers: `);
        console.error(e);
      }
    }
    // 执行actions处理函数
    const result =
      entry.length > 1
        ? Promise.all(entry.map((handler) => handler(payload)))
        : entry[0](payload);
    // 返回所有被触发action promise
    return new Promise((resolve, reject) => {
      result.then(
        (res) => {
          try {
            this._actionSubscribers
              .filter((sub) => sub.after)
              .forEach((sub) => sub.after(action, this.state));
          } catch (e) {
            if (__DEV__) {
              console.warn(`[vuex] error in after action subscribers: `);
              console.error(e);
            }
          }
          resolve(res);
        },
        (error) => {
          try {
            this._actionSubscribers
              .filter((sub) => sub.error)
              .forEach((sub) => sub.error(action, this.state, error));
          } catch (e) {
            if (__DEV__) {
              console.warn(`[vuex] error in error action subscribers: `);
              console.error(e);
            }
          }
          reject(error);
        }
      );
    });
  }
}
```

### 响应式处理

vuex4 的响应式核心在 resetStoreState 初始化 store 中：

```js
import { reactive } from "vue";
export function resetStoreState(store, state, hot) {
  // 获取就state
  const oldState = store._state;
  store.getters = {};
  // 数据响应式
  store._state = reactive({
    data: state,
  });
  // 开发环境下，热更新
  if (oldState) {
    if (hot) {
      store._withCommit(() => {
        oldState.data = null;
      });
    }
  }
}
```

### mutation 与 action

有个面试题，问为什么 action 是异步的，而 mutation 是同步的。搜了半圈没找到答案，最后在知乎找到：vuex 的来源类似于 redux，其中 mutation 对应 reducer，而 reducer 是纯函数，故 mutation 做同步处理（？）。
yxx 的回答：mutation 做异步不访问 devtools 处理，因此单独抽出来一层 actions 做处理：
