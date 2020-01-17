---
title: tree树形结构数据处理总结
date: 2020-01-17 09:01:01
tags: [算法]
categories: js
---

### 业务中经常碰到对 tree 层级结构的数据处理，现总结如下

#### 树形结构转为扁平化一维数组

<!--- more --->

```
//通过栈思想实现数据的扁平化
    let tree = [{
      id: 1,
      name: "第一级数据",
      children: [{
        id: 2,
        name: "第二级数据",
        children:[{
            id:5,
            name:'第2-1级数据'
        }]
      }, {
        id:3,
        name: "第3级数据"
      }, {
        id: 4,
        name: "第4级数据"
      }]
    }];
    function treetoLsit(tree){
        let result,arr = [];
        let arr = arr.concat(tree);
        white(arr.length){
             let first =  arr.shift(); // 删除数组下标索引为0的第一项，并获取（会改变原数组）
            if(first.chilren){
                arr = arr.concat(first.chilren); //往arr数组中塞新的children数据
                delete first['children];  //删除children属性
            }
             result.push(first);
        }

    return result;
    }
```

#### 扁平化一维数组转为树形结构

```
// 通过id和pid关联关系，形成tree结构
var nodes = [
  {"id":1,"pId":0,"name":"父节点1 - 展开","open":true},
  {"id":11,"pId":1,"name":"父节点11 - 折叠"},
  {"id":12,"pId":1,"name":"父节点12 - 折叠"},
  {"id":13,"pId":1,"name":"父节点13 - 没有子节点"},
  {"id":2,"pId":0,"name":"父节点2 - 折叠"},
  {"id":21,"pId":2,"name":"父节点21 - 展开","open":true},
  {"id":22,"pId":2,"name":"父节点22 - 折叠"},
  {"id":23,"pId":2,"name":"父节点23 - 折叠"},
  {"id":3,"pId":0,"name":"父节点3 - 没有子节点"}
];
function buildTree(array,id,parent_id) {
    // 创建临时对象
    let temp = {};
    // 创建需要返回的树形对象
    let tree = {};
    // 先遍历数组，将数组的每一项添加到temp对象中
    for(let i in array) {
        temp[array[i][id]] = array[i];
    }

    /*  上面也可以写成
    for(let i=0;i<array.length;i++) {
        temp[array[i][id]] = array[i];
    }
    */

    // 遍历temp对象，将当前子节点与父节点建立连接
    for(let i in temp) {
        // 判断是否是根节点下的项
        if(temp[i][parent_id] !== 0) {
             if(!temp[temp[i][parent_id]].children) {
                 temp[temp[i][parent_id]].children = new Array();
             }
             temp[temp[i][parent_id]].children.push(temp[i]);
        } else {
            tree[temp[i][id]] = temp[i];
        }
    }

    /*  上面也可以写成
    for(let i=0;i<array.length;i++) {
        temp[array[i][id]] = array[i];
    }
    */
    return tree;
}

```

#### 对树形数据结构进行针对性修改操作

```
// 通过id匹配当前项插入新数组
var data = [{
      id: 1,
      name: "第一级数据",
      children: [{
        id: 4,
        name: "第二级数据"
      }, {
        id: 5,
        name: "第二级数据"
      }, {
        id: 6,
        name: "第二级数据"
      }]
    }];
     function dataRecursion(oldData, id,newArr) {
        let newData = [];let o = {};
        let tempObj =  {};
        tempObj['children']= newArr;
        oldData.map(function(item, index) {
          if (item.id == id) {
             o = Object.assign(item,tempObj);
             item = o;
          } else if (item.children && item.children.length > 0) {
            dataRecursion(item.children, id,newArr);
          }
          newData.push(item);
        });
        return newData;
      }
    let newArr = [{name:'新插入的数据',id:9999}]; 
      let result = dataRecursion(data, 5,newArr);
     console.log(result)
```
