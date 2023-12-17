---
title: JavaScript数组递归：构建ElementUI级联选择器的树形数据
tags:
  - ElementUI
  - 递归
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
date: 2022-09-21 22:14:44
---
## 前端处理

通常，我们前端在开发管理后台的时候，会选择 `vue+element` 这样的技术栈去实现。但我们后端给的数据格式通常并不符合 `element` 的参数数据要求。比如，级联选择器 `cascader` 的参数数据格式如下：

```json
[{
	"label": "第一级",
	"value": 2,
	"children": [{
		"label": "第二级",
		"value": 4,
		"children": [{
			"label": "第三级",
			"value": 13
		}, {
			"label": "测试3",
			"value": 14
		}]
	}]
}, {
	"label": "2",
	"value": 3,
	"children": [{
		"label": "4",
		"value": 5
	}]
}, {
	"label": "测试分类2",
	"value": 11,
	"children": [{
		"label": "测试分类0",
		"value": 12,
		"children": [{
			"label": "测试分类3",
			"value": 15
		}]
	}]
}]
```

而我们后端在输出接口的时候，是极少采用这种数据格式的。比如，一般叫 id|name|child 这样的字段名。

我们知道，cascader 是支持这样的别名参数设计的，因此我们使用时也没什么问题。

但是我今天遇到的一个状况是，后端直接给出了一个一维数组，换句话说，这位兄弟是直接查了一下数据表，把所有的数据通过一个数组直接给我返回了。而至于我怎么使用这个数据，他就不考虑了，因为他还有其他业务需要开发。

后端返回的数据格式如下：

```json
[{
	"catid": 2,
	"parentId": 0,
	"catname": "第一级",
}, {
	"catid": 3,
	"parentId": 0,
	"catname": "2",
}, {
	"catid": 4,
	"parentId": 2,
	"catname": "第二级",
}, {
	"catid": 5,
	"parentId": 3,
	"catname": "4",
}, {
	"catid": 12,
	"parentId": 11,
	"catname": "测试分类0",
}, {
	"catid": 11,
	"parentId": 0,
	"catname": "测试分类2",
}, {
	"catid": 13,
	"parentId": 4,
	"catname": "第三级",
}, {
	"catid": 14,
	"parentId": 4,
	"catname": "测试3",
}, {
	"catid": 15,
	"parentId": 12,
	"catname": "测试分类3",
}]
```

嗯，理解。不就是个简单的递归嘛！我一会儿就写好了。但我担心后面其他地方还有这样类似的接口输出，那我总是写递归不就很麻烦了吗？因此，将此方法抽离，并加了一些扩展，最终方法代码如下：

```js
export const makeElementTree = (params) => {
  // 将参数拿出来，不喜欢 params.xxx 的调用方式
  const { pid, list, pidFiled, labelFiled, valueFiled } = params
  // 构建一个内部函数，用于实现递归
  const makeTree = (pid, arr) => {
    const res = []
    arr.forEach(i => {
      if (i[pidFiled] === pid) {
        // 自己调用自己，递归查归属于自己的 children
        const children = makeTree(i[valueFiled], list)
        // 将原有的数据按照 element 的格式进行重构
        const obj = {
          label: i[labelFiled],
          value: i[valueFiled]
        }
        // 如果有 children 则插入 obj 中
        if (children.length) {
          obj.children = children
        }
        res.push(obj)
      }
    })
    return res
  }
  return makeTree(pid, list)
}
```

在业务代码中，我们首先引入我们的方法，然后传进去参数就可以啦，如下演示：

```js
const treeCats = makeElementTree({
  pid: 0,               // 顶级分类的 pid 为 0
  list: data,           // 将原始数组参数穿进去
  pidFiled: 'parentId', // 在数组对象中，pid 字段名为 parentId
  labelFiled: 'catname',// 我们想要的 label 字段名为 catname
  valueFiled: 'catid'   // 我们想要的 value 字段名为 catid
})
console.log(treeCats)
```

## 后端数据处理

如果是后端直接处理后返回，则递归如下：

```python
def makeTree(pid, arr):
    res = []
    for i in arr:
        if i['pid'] == pid:
            rep = makeTree(i['id'], arr)
            if len(rep) != 0:
                i['children'] = rep
            res.append(i)
    return res
res = makeTree(0, sourceList)
```

**最后，在编程中，慎用递归！！！**