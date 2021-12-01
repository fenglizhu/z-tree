<template>
  <div>
    <div class="tree-item-text" :style="{'padding-left': chlidrenData.level == 1 ? '10px' : ('18' * ( chlidrenData.level -1 ) + 'px')}" @click="showHideHandle(chlidrenData)">
    <div class="select-text">
      <span :class="[ 'select-icon', { 'selected': chlidrenData.selected == 1, 'half-selected': chlidrenData.selected == -1 } ]" 
        @click.stop="getCurrentNode(chlidrenData, parentData, chlidrenData.selected)">
      </span>
      <span>{{chlidrenData.tName}}</span>
    </div>
    <span v-if="chlidrenData.treeData" :class="['select-more', {'show-more': chlidrenData.toggle}]">
      <!-- <img :src="chlidrenData.toggle ? require('../assets/xiala.png') : require('../assets/gengduo.png')" alt=""> -->
    </span>
  </div>
  <div class="tree">
    <TreeItem v-show="chlidrenData.treeData && chlidrenData.toggle" v-for="item in chlidrenData.treeData" 
      @child-click="childClick"
      :key="item.id" 
      :chlidrenData="item" 
      :parentData="parentData"
      @change-data="changeData"
      @current-node="currentNode" 
    />
  </div>
  </div>
  
</template>
<script lang="ts">
import { defineComponent } from 'vue';
export default defineComponent({
  name: 'TreeItem',
  props: {
    chlidrenData:{
      type: Object
    },
  },
  setup( props, { emit , attrs}) {
    let parentData = attrs.parentData;
    
    let showHideHandle = (item:any) => {
      item.toggle = !item.toggle
    }
    /**
     * 设置当前状态说明
     * selected
     * 0  不选中
     * 1  选中
     * -1 不全选中
     */
    let getCurrentNode = (item:any, treeData: any[], selected: number) =>{

      emit('current-node', item);

      selectHandle(item, treeData, selected);

      emit('child-click', item, treeData, item.selected);
    }
    let selectHandle = (item:any, treeData: any[], selected: number) => {
      /**
       * 设置当前状态说明
       * selected
       * 0  不选中
       * 1  选中
       * -1 不全选中
       */
      if(item.parentAllSelect) {
        if(selected == 0) {
          item.selected = 1;
        } else {
          item.selected = 0;
        }
      }else {
        if(selected == 0 || selected == -1) {
          item.selected = 1;
        } else {
          item.selected = 0;
        }
      }

      // 设置子集
      if(item.treeData) {
        for (let i = 0; i < item.treeData.length; i++) {
          selectHandle(item.treeData[i], treeData, selected);
        }
      }
    }

    /**
     * data： 当前选中数据
     * treeData 总数据
     * flag
     * selected 当前状态
     * 
     */
    let childClick = (data:any, treeData:any, selected:number) =>{
      
      if(data.parentId) {
        if (data.parentAllSelect) {
          findParent(data.parentId, treeData, treeData, selected);
        } else {
          findParentFlase(data.parentId, treeData, treeData, selected);
        }
      }

      emit('change-data', treeData)
    }

    let changeData = (treeData:any) =>{
      emit('change-data', treeData)
    }

    let currentNode = (data:any) =>{
      emit('current-node', data)
    }
    
    /**
     * parentId 父级元素的id
     * treeData 当前的treeData数据
     * allData 所有数据
     * selected 选中不选中
     */
    let findParent = (parentId:string, treeData:any, allData:any , selected:number) => {
      if(treeData) {
        
        for (let i = 0; i < treeData.length; i++) {
          if(parentId == treeData[i].id) {
            // 先判断是不是选中。如果是，直接往上找设置
            if(selected) {
              treeData[i].selected = selected;
              findParent(treeData[i].parentId, allData, allData, selected);
            // 如果不是，
            // 两种情况：1 子节点全部没选中，那么父节点需要取消选中
            } else {
              let isAllSelect = false;
              if(treeData[i].treeData) {
                for (let j = 0; j < treeData[i].treeData.length; j++) {
                  if (treeData[i].treeData[j].selected) {
                    isAllSelect = true;
                  }
                }
              }
              if(!isAllSelect) {
                treeData[i].selected = selected;
                findParent(treeData[i].parentId, allData, allData, selected);
              }
            }
            break;
          } else {
            findParent(parentId, treeData[i].treeData, allData, selected);
          }
          
        }
      }
    }

    /**
     * parentId 父级元素的id
     * treeData 当前的treeData数据
     * allData 所有数据
     * selected 选中不选中
     */
    let findParentFlase = (parentId:string, treeData:any, allData:any , selected:number) =>{
      if(treeData) {
        for (let i = 0; i < treeData.length; i++) {
          if(parentId == treeData[i].id) {
            // 选中。如果是，判断下面的子集数据是否有数据没选中
            // 如果有没有全选中的数据，那么就是半选中状态，否则就是选中状态
            // 取消选中
            // 如果有一个是不选中，那么就是半选中，否则就是不选中 // 只要有一个选上 那就是半选中
            if(selected == 1 || selected == 0) {
              let isAllSelect = false;
              if(treeData[i].treeData) {
                for (let j = 0; j < treeData[i].treeData.length; j++) {

                  // 此代码逻辑跟下面的else 一样，所以整合在一起了
                  if ((selected && treeData[i].treeData[j].selected != 1) || (!selected && (treeData[i].treeData[j].selected == 1 || treeData[i].treeData[j].selected == -1))) {
                    isAllSelect = true;
                  }

                }
              }
              // 判断只要子节点有一个没有选中，那么就是半选择状态 ,否则就是选中状态
              if(isAllSelect) {
                treeData[i].selected = -1;
                findParentFlase(treeData[i].parentId, allData, allData, treeData[i].selected);

              }else {
                treeData[i].selected = selected;
                findParentFlase(treeData[i].parentId, allData, allData, treeData[i].selected);
              }
            // 如果是半选中状态，那么父级数据也要是半选中状态
            } else if (selected == -1) {
              treeData[i].selected = -1;
              findParentFlase(treeData[i].parentId, allData, allData, treeData[i].selected);
            }
            break;
          } else {
            findParentFlase(parentId, treeData[i].treeData, allData, selected);
          }
          
        }
      }
    }

    return {
      showHideHandle,
      getCurrentNode,
      childClick,
      changeData,
      currentNode,
      parentData
    }
  }
})
</script>