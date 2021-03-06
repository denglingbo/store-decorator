## vue-store-decorator

### store/modules/demo.{ts|js}

```
import StoreDecorator, {
  Action,
  Mutation,
  Getter,
  AutoLoading,
  AutoErrors,
} from 'vue-store-decorator';

@StoreDecorator
@AutoLoading
class Store {
  state() {
    return {
      name: 'nobody',
      level: {
          age: 'level age'
      },
      age: 0,
    }
  }

  @Action()
  unautoDispatch({ commit }) {
    console.log('unautoDispatch');
    
    commit('xxx', payload);
  }

  @Getter
  info(state) {
    return `Name: ${state.name} - Age+1: ${state.age + 1}`;
  }

  @Mutation('name')
  changeName(state, { name }) {
    return name;
  }

  @Mutation('level.age')
  changeAge(state, payload) {
    return payload.xxx;
  }

  @Action('changeName', { autoLoading: false })
  async fetch(context, params) {
    const res: any = await mockReq(params);
    return res.data;
  }

  @Action('changeAge')
  async updateAge(context, params) {
    const res: any = await mockReq(params);
    return res.data;
  }

  @Action('changeName')
  async update(context, params) {
    return await mockReq(params);
  }

  @Action(['changeAge', 'changeName'])
  async updateAll(context, params) {
    return await mockReq(params);
  }
}

export default new Store();
```

### views/demo.vue

* `template`

```
<div class="hello">
  <button @click="updateName">Update Name, (loading: {{ loadings.update }})</button>
  <button @click="handleAge">Update Age, (loading: {{ loadings.updateAge }})</button>
</div>
```

* `script lang="ts"`

```
@Component
export default class App extends Vue {
  @Getter('auth/loadings') loadings: any
  @Action('auth/update') update: any
  @Action('auth/updateAge') updateAge: any

  updateName() {
    this.update({
      name: 'demo name'
    })
  }

  handleAge() {
    this.updateAge({
      age: 32,
    })
  }
}
```

#### `@StoreDecorator return {}`
```
{
  namespaced: true,
  state: ...,
  mutations: ...,
  actions: ...,
  getters: ...,
}
```

#### `state 使用函数 return`

#### `@Action(param) {string|array}`
包裹的 Method name 对应 *.vue `Vuex-class: @Action('moduleName/methodName')`
param 为该 store.ts 中 @Mutation() 包裹的 Method name
注意：此处需要 return 一个 Promise

#### `@Mutation(param) {string}`
包裹的 Method name 对应 *.vue `Vuex-class: @Mutation('moduleName/methodName')`
param 为对应的 state 中的 key，此处可以是 `Key` or `Object Path`，例如: @Mutation('key'), @Mutation('key1.key2')
注意：此处需要 return 一个结果

#### `@Getter`
包裹的 Method name 对应 *.vue `Vuex-class: @Getter('moduleName/methodName')`
注意：此处需要 return 一个结果

#### `@AutoLoading` 自动为 @Action 创建 loadings 对象
对应 .vue `@Getter('moduleName/loadings') loadings: any`
注意：store.ts 中的 @Action 包裹的 Method name 为 loadings 的 key

### `1. 默认方式 vuex`

### `2. vuex-class`
[vuex-class](https://github.com/ktsn/vuex-class/)
参照 views/demo.vue 中的写法


#### Required packages
* [vue-class-component](https://github.com/vuejs/vue-class-component/)
* [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator)
