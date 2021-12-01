<template>
<div class="container">
  
  <div class="mode-btn">
    <div class="mode-tips">模式一：选中子集某一个，父级选中<br />模式二：全部选中子集，父级选中，否则半选中</div>
    <button @click="toggle">{{parentAllSelect ? '模式一' : '模式二'}}</button>
  </div>
  <Tree 
    :treeData="state.treeData" 
    :parentAllSelect="parentAllSelect"
    @get-current-node="getCurrentNode"
    @change-node="changeNode"
   />
</div>
  
</template>
<script lang="ts">
import { defineComponent, reactive, ref } from 'vue';
import Tree from './components/Tree.vue'
export default defineComponent({
  name: 'App',
  components: {
    Tree
  },

  setup() {
    let parentAllSelect = ref(true);
    let state = reactive({
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
                  tName: '一级1_二级2_三级1',
                  treeData: [
                    {
                      tName: '一级1_二级2_三级1_四级1'
                    },
                    {
                      tName: '一级1_二级2_三级1_四级2',
                      treeData: [
                        {
                          tName: '一级1_二级2_三级1_四级2_五级1',
                          treeData: [
                            {
                              tName: '一级1_二级2_三级1_四级2_五级1_六级1'
                            },
                            {
                              tName: '一级1_二级2_三级1_四级2_五级1_六级2'
                            }
                          ]
                        },
                        {
                          tName: '一级1_二级2_三级1_四级2_五级2'
                        }
                      ]
                    }
                  ]
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
    })
    let toggle = () =>{
      parentAllSelect.value = !parentAllSelect.value;
    }
    let getCurrentNode = (node:any)=>{
      console.log('当前选中的节点：', node);
    }

    let changeNode = (data:any) =>{
      console.log('总数据：', data);
    }
    return {
      state,
      parentAllSelect,
      toggle,
      getCurrentNode,
      changeNode
    }
  }
})
</script>
<style>
  * {
    padding: 0;
    margin: 0;
  }
  .container {
    width: 500px;
    height: 500px;
    margin: auto;
  }
  .mode-btn{
    text-align: center;
    padding: 10px;
    background: rgb(255,252,245);
  }
  .mode-btn button {
    outline: none;
    border: none;
    padding: 10px 20px;
    background: rgba(41, 109, 41, 0.76);
    color: white;
    cursor: pointer;
  }
  .mode-tips{
    padding: 5px;
    text-align: left;
    color: rgb(54, 54, 54);
    font-size: 13px;
  }
</style>