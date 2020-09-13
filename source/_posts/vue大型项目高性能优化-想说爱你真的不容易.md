---
title: 想说爱你不容易--vue大型项目高性能优化
date: 2020-09-13 10:40:59
categories: 个人博客
tags:
 - vue
 - 性能优化
 - vue性能优化
 - 前端性能优化
preview: 100
toc: true
---

## 一、背景

&emsp;&emsp;目前公司的```电子合同```采用```表单设计器```+```合同业务```配合实现，做了半年多后终于上线，但是下边员工普遍反映卡顿，甚至卡死，爆栈。尤其是新增和修改合同页面，因为这部分数据量大，逻辑复杂，很容易崩溃，所以决定进行性能优化。

## 二、业务场景介绍

&emsp;&emsp;先来了解一下我们是怎么实现：

&emsp;&emsp;1. 因为我们公司合同变换频繁，条款之间还有逻辑，所以做了个```基础服务```（说白了就是组件库），为合同提供模板

&emsp;&emsp;2. 表单设计器作为基础服务，打包成了组件库，嵌入到合同项目，包括合同生成组件（拖拽生成合同模板）和合同预览组件（加载数据库中的合同模板数据）

&emsp;&emsp;3. 合同项目有一个模块管理页面，可以对多个模板进行维护，比如可以选择启用哪个模板。

&emsp;&emsp;4. 合同的管理员负责维护模板，可以用表单设计器拖拽生成合同模板，提交后落入数据库，每个合同类型可以同时启用一个模板。

&emsp;&emsp;5. 最终下边员工用的就是启用的模板（尤其是这部门卡顿） 

**下面是电子合同的宏观泳道图：**
![image](https://img-blog.csdnimg.cn/20200216200900531.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

 ## 三、页面介绍
 1. 合同模板管理页
 ![image](https://img-blog.csdnimg.cn/20200910105834157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)
 2. 新增模板页面
 ![image](https://img-blog.csdnimg.cn/20200910105956622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)
 3. 新建合同页面
![image](https://img-blog.csdnimg.cn/202009101101566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)
 4. 合同填写页面
 ![image](https://img-blog.csdnimg.cn/20200911134425140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)
 
&emsp;&emsp;好了，基本的业务逻辑和页面就介绍这么多，特别卡顿的页面就是第四个页面，下面我们分析一下卡顿的原因。

 ## 四、卡顿分析

&emsp;&emsp;1. 首先就是表单设计器的问题最严重，因为每一个组件需要很多配置项才能够支撑组件的渲染，而一个合同是由上千个组件组成，经过测试，一个合同模板需要5MB的存储空间(数据库用的是MongoDB，存储格式为字符串，几乎不影响)，下面是一个输入框的配置

![image](https://img-blog.csdnimg.cn/20200910103459458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

&emsp;&emsp;2. 表单设计器的实现用了大量的闭包管理业务，我们都知道，闭包是特别耗内存的。

&emsp;&emsp;3. 合同模板巨复杂，由上万个组件拼接而成，我把模板数据down下来看了一下，大约是16000多个组件，大小为3.4MB。

&emsp;&emsp;4. 因为表单设计器中包括```id,model,事件id```都是前端随机生成的，采用```随机字符串+时间戳```的形式，一共46位。

&emsp;&emsp;5. 合同项目属于大型项目，业务场景及其复杂，包括合同管理，附件管理，合同列表，新增页面，审批页面等等，我计算了一下，光路由页面就有三十多个，```页面，组件，样式,业务```巨多，如果不做处理，不卡才怪
 


## 五、性能优化
#### 1. 第一次尝试
&emsp;&emsp;说一下我的优化思路：首先，电子合同由表单设计器和合同业务两个项目共同完成，合同模板加载慢的原因是浏览器渲染了大量的模板数据，这些模板数据是由多个组组成的（大约12个），我第一想到的就是```分组渲染```，先加载一个组，先让用户看到页面，然后在继续加载，一个一个,最终加载完成。这也是被大家认可的方案。

&emsp;&emsp;然后我就开始实现这个分组渲染，做了大概有二十多天吧，一点效果没出来。

&emsp;&emsp;先看一下渲染的代码：
```
<template v-show="itemManage==='group'">
  <preview-item-template v-for="(item) in domainNodeList"
                        :key="item.id"
                        :formNode="item"
                        :parent="domainNodeList">
  </preview-item-template>
</template>
```
&emsp;&emsp;上面就是所有组加载的代码，这是一个```v-for```,做分组渲染，我想到使用```vue的异步组件```实现，但是这是一个循环，所有的组件注册的都是同一个名字，这显然是不能用异步组件的，除非注册的是不同名字的组件，但是我想了很长时间都做出来效果，所以这二十多天，失败了。

#### 2.第二次尝试
&emsp;&emsp;上边说了，模板加载慢是因为浏览器渲染了大量的数据，我们知道，js是单线程的，也就是说，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。因此js处理数据的能力有限，所以在朋友的建议下调研了一把```webworker```。

&emsp;&emsp;webworker的作用，就是为js创造多线程环境，允许主线程创建Worker线程，将一些任务分配给后者运行。在主线程运行的同时，Worker线程在后台运行，两者互不干扰。

&emsp;&emsp;看了一把文档我第一时间觉得这个方案不可行。说到底我们就是想要webworker为我们开辟县城用来处理大量数据，但是webworker处理的大数据，不是指数据量非常大，而是要从计算量来看，通常用时不能控制在毫秒级内的运算都可以考虑放在web worker中执行。而我们的合同模板数据恰恰是数据量大，并不需要做特别大的运算。

&emsp;&emsp;第二次尝试失败。

#### 3.第三次尝试

&emsp;&emsp;后来在同事的建议下决定采用```ssr```,也就是```服务端预渲染```。我们平常写的vue项目打包后生成```dist```,运维会把这个文件夹放在服务器中，我们看到的页面其实就是生成执行的```render函数```，这是比较耗时的。

&emsp;&emsp;所谓的服务端渲染，就是在```服务端```生成静态页面，然后交给```客户端```渲染。

&emsp;&emsp;自己从零搭建一套服务端渲染的应用是相当复杂的，所以我最终选用了```nuxt```框架。关于nuxt框架我不多做介绍，可以自己去看文档[（传送门）](https://nuxtjs.org/)。这个框架有自己的脚手架，也是vue官方推荐的。

&emsp;&emsp;经过了一周的时间，完成了从vue向nuxt的迁移，大部门页面速度有了明显的提升。

&emsp;&emsp;**除了我们想优化的新增合同页面。**

&emsp;&emsp;经过分析，合同项目用到的组件库有```element-UI```和我问自己的表单设计器，element只有部门组件支持ssr，像是```表格和树```是不支持ssr的，所以就不存在服务端渲染了。

&emsp;&emsp;我也曾尝试过弄一把表单设计器，让它支持ssr，但是并没有效果，如果有谁知道，可以联系我。

&emsp;&emsp;很显然，第三次也失败了。

#### 4.第四次尝试

&emsp;&emsp;命运总是很捉弄人，优化了一个多月的合同，速度并没有显著的提升，领导很着急，我也很着急。

&emsp;&emsp;突然有一天，我在回家的途中，记得那天风雨交加，雷霆大作，一声巨雷轰天响，把我好的idea都劈出来了。我一下子想到了分组加载的实现。

先来看一把代码的实现（只展示了部分代码）：


```
<template>
  <div class="dialog-preview" v-show="!formLoading">
      <el-form  ref="previewForm" onsubmit="return false"
                :size="formSettingState.componentSize"
                @hook:mounted="formMounted"
                :model="formModels">

        <template v-show="itemManage==='group'">
          <preview-item-template v-for="(item) in cutDomainNodeList.one"
                                :key="item.id"
                                :formNode="item"
                                :parent="cutDomainNodeList.one">
          </preview-item-template>
        </template>
        <template v-if="itemManage==='group' && formLoadingTwo">
          <preview-item-template v-for="(item) in cutDomainNodeList.two"
                                :key="item.id"
                                :formNode="item"
                                :parent="cutDomainNodeList.two">
          </preview-item-template>
        </template>
         <template v-if="itemManage==='group' && formLoadingThree">
          <preview-item-template v-for="(item) in cutDomainNodeList.three"
                                :key="item.id"
                                :formNode="item"
                                :parent="cutDomainNodeList.three">
          </preview-item-template>
        </template>
        </template>
      </el-form>
  </div>
</template>
<script>
export default {
    data() {
        return {
          formLoading: true,
          formLoadingTwo: false,
          formLoadingThree: false
        }
    },
    computed: {
        cutDomainNodeList () {
          let { domainNodeList } = this;
          let length = domainNodeList.length;
          if ( length <= 4 ) {
            return {
              one: domainNodeList
            }
          }else {
            return {
              one: domainNodeList.filter((el, index) => index <=2 ),
              two: domainNodeList.filter((el, index) => index>2 && index <=5 ),
              three: domainNodeList.filter((el, index ) => index > 5)
            }
        }
    },
    methods: {
        formMounted () {
          setTimeout(() => { this.formLoading = false },  500);
          setTimeout(() => { this.formLoadingTwo = true },  700);
          setTimeout(() => { this.formLoadingThree = true},  900);
        }
    }
}
```
**分块加载实现思路：**

&emsp;&emsp; 1. 首先我把模板数据这个list利用计算属性先做了个判断，如果数组长度小于4，证明数据量较小，不需要分块加载，如果大于4证明数据量大，需要进行分块加载

&emsp;&emsp; 2. 分块加载是根据数组索引过滤的，第一块是0-2组，第二块是2-5组，第三块是索引大于5的（也可以分割的跟细），然后再页面中分别遍历渲染

&emsp;&emsp; 3. 看一下```html```中的```el-form```这个标签，里边有个```@hook:mounted="formMounted"```这句话，```@hook:```+```生命周期```代表在这个生命周期时执行，我们等```mounted```执行完延时500mm开始加载第一块，700mm加载第二块，900毫秒加载第三块，这样分块加载的效果就出来了。

## 六、其他方面优化

&emsp;&emsp;  首先添加了骨架屏组件，让用户在等待的时候能看到过渡效果。

&emsp;&emsp; 上面提到，合同模板大约在```3.4MB```，这个就是个纯```json```,让浏览器一下子加载这个么大的数据难免卡顿，所以我就在想能不能优化一下模板大小，从而能够提升加载速度。

&emsp;&emsp; 表单设计器中包括```id,model,事件id```都是前端随机生成的，采用```随机字符串+时间戳```的形式，一共46位，一个英文字符就是一个字节，这就是46个字节，所以我们可以缩短一下随机数的长度，从而减少一下模板大小。

&emsp;&emsp; 最终选用了26位随机数，我算了一下，大约能减少一半大小。

&emsp;&emsp; 后来我们让测试人员新生成了一个模板，果然，新模板大小```1.44MB```，缩短了一倍还多。

&emsp;&emsp; 其他方面，我们知道表单设计器有些配置做的不到位，所以管理员不得不换个别的方式拖拽模板，所以我们加了一些配置项，从而使管理员可以少拖拽一些组件。这部分优化下来，模板大小大约减少了```300多kb```.

&emsp;&emsp; 我们还可以优化一下表单设计器的代码，把闭包换个实现方式，应该也能提高加载速度，后续会做这些。

&emsp;&emsp; 合同业务项目也优化了一些接口，代码，前后端交互方式，以及页面的交互方式提高了性能和视觉效果。


## 七、总结

&emsp;&emsp; 这是我第一次费这么大劲做vue项目的性能优化，虽然坎坷，但也留下了好结果，我们从最初加载需要50秒甚至一分钟，到现在10秒左右就能加载成功，速度提高可近5倍。

&emsp;&emsp; 今日成果，虽数月，但众人拾柴，得以燎原，此非一人之功，谢而不及。






