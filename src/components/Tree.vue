<template>
  <div class="tree">
    <TreeItem v-for="item in state.treeData" :key="item.id" :class="['tree-item']" 
    :chlidrenData="item" 
    :parentData="treeData"
    @change-data="changeData"
    @current-node="currentNode" />
  </div>
</template>
<script lang="ts">
import { defineComponent, reactive, watch } from 'vue';
import TreeItem from './TreeItem.vue'
export default defineComponent({
  name: 'Tree',
  props: {
    treeData: {
      type: Array
    },
    parentAllSelect: {
      type: Boolean,
      default: true
    }
  },
  components: {
    TreeItem
  },
  setup(props, { emit }) {
    const getInitalState = () => {
      return props.treeData
    }
    let state:any = reactive({
      treeData: getInitalState()
    })
    let changeData = (data:any) =>{
      state.treeData = data;
      // 只要改变，都触发
      emit('change-node', data)
    }
    let currentNode = (node:any) =>{
      // 选中和不选中，都触发
      if(node.selected != 1) {
        emit('get-current-node', node);
      }
    }
    
    let setIdParentId = (data:any[], parnetId: number, level: number) =>{
      for (let i = 0; i < data.length; i++) {
        data[i].parentId = parnetId + '';
        data[i].id = parnetId ? `${parnetId}_` + (i + 1) : (i + 1) + '';
        data[i].selected = 0;
        data[i].toggle = false;
        data[i].level = level;
        data[i].parentAllSelect = props.parentAllSelect;
        if(data[i].treeData) {
          setIdParentId(data[i].treeData, data[i].id, level+1)
        }
      }
    }

    // 初始化数据
    setIdParentId(state.treeData, 0, 1);

    watch(()=>props.parentAllSelect,(values)=>{
      setIdParentId(state.treeData, 0, 1);
    })
    
    return {
      changeData,
      currentNode,
      state
    }
  }
})
</script>
<style>
  .tree-item-text{
    display: flex;
    justify-content: space-between;
    padding: 10px 10px;
    cursor: pointer;
    border-bottom: 1px solid #f8f8f8;
    font-size: 13px;
    color: #666;
  }
  .tree-item-text:hover{
    background: #f4f4f5;
  }
  .tree-item-text .select-text{
    display: flex;
    align-items: center;
  }
  .tree-item-text .select-text .select-icon {
    width: 12px;
    height: 12px;
    margin-right: 10px;
    border: 1px solid rgb(231,231,231);
    border-radius: 4px;
    background: white;
  }
  .tree-item-text .select-text .select-icon.selected,
  .tree-item-text .select-text .select-icon.half-selected{
    position: relative;
    background: rgba(41, 109, 41, 0.76);
    border: 1px solid rgba(41, 109, 41, 0.76);
  }
  
  .tree-item-text .select-text .select-icon.selected:after{
    position: absolute;
    left: 50%;
    top: 50%;
    content: '\2713';
    color: white;
    font-size: 10px;
    transform: translate(-50%, -50%);
  }
  .tree-item-text .select-text .select-icon.half-selected:after{
    position: absolute;
    left: 50%;
    top: 50%;
    content: '';
    width: 60%;
    height: 2px;
    background: white;
    transform: translate(-50%, -50%);
  }
  .tree-item-text .select-more, .tree-item-text .show-more{
    position: relative;
    width: 15px;
    height: 15px;
  }
  .tree-item-text .select-more::after{
    position: absolute;
    left: 0;
    top: 3px;
    content: '';
    width: 0;
    height: 0;
    border-right: 6px solid rgb(199,199,199);
    border-top: 5px solid transparent;
    border-bottom: 5px solid transparent;
  }
  .tree-item-text .show-more::after{
    content: '';
    transform: rotate(-90deg);
  }
</style>