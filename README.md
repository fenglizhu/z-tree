# vuetypescript

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```


### 数据结构
```
treeData: [
  { 
    tName: '一级1',
    treeData: [
      {
        tName: '一级1_二级1'
      },
      {
        tName: '一级1_二级2',
        treeData: [
          {
            tName: '一级1_二级2_三级1'
          },
          {
            tName: '一级1_二级2_三级2'
          }
        ]
      }
    ]
  },
  { 
    tName: '一级2',
    treeData: [
      {
        tName: '一级2_二级1'
      },
      {
        tName: '一级2_二级2'
      }
    ]
  },
  { 
    tName: '一级3'
  }
]
```

### 使用
```
<template>

  <Tree 
    :treeData="treeData" 
    :parentAllSelect="parentAllSelect"
    @get-current-node="getCurrentNode"
    @change-node="changeNode" />

</template>
<script>
    let getCurrentNode = (node:any)=>{
      console.log('当前选中的节点：', node);
    }

    let changeNode = (data:any) =>{
      console.log('总数据：', data);
    }
</script>
```
props：
  treeData：树形数据
  parentAllSelect ：使用哪种模式 
    true：模式一  
    false：模式二

事件：
  @get-current：选中事件，返回当前选中节点
  @change-node：数据改变事件，返回全部数据

### 项目演示地址
待定
