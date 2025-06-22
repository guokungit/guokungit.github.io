---
title: react前端下载后端返回的文件方法（url、二进制流）
date: 2022-05-22 22:41:02
tags: 前端调接口下载
categories: 下载
comments: true
---
### 前端下载文件有两种方式：
1、通过后端返回的url，前端通过window.open(url);
2、后端返回的是二进制流，通过a标签实现下载。
##### 方法一：后端将静态资源放在了cdn上，返回cdn上的网址给前端。前端的window.open方法打开url时，浏览器判断当前url是资源还是页面，当是资源时进行下载，否则展示页面。
##### 方法二：后端返回二进制流。将二进制流封装成blob，这种的不推荐，当当前网速不好时，下载的数据易出错。
```
axios({
      method: 'post',
      url: '/api/trust/auditDownload', //此处放hobber的内部的url
      data: params,
      responseType: 'arraybuffer', // 需要制定返回的类型，否则下载的文件会乱码
    }).then(res => {
      // 假设 data 是返回来的二进制数据
      const data = res.data;
      const url = window.URL.createObjectURL( // type指定下载的文件的格式
        new Blob([data], {
          type:
            'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
        }),
      );
      const link = document.createElement('a');
      link.style.display = 'none';
      link.href = url;
      link.setAttribute('download', 'excel.xlsx'); // 设置下载的文件名
      document.body.appendChild(link); 
      link.click(); // 触发a标签
      document.body.removeChild(link); // 删除标签
    });
```
##### 方法三、将二进制数据封装为文件读取FileReader对象
```
// utils.js
export const download = (res) => {
  // 创建文件对象
  let reader = new FileReader()
  // 监听读取完毕
  reader.onloadend = function () {
    // 返回base64位的文件内容
    let url = reader.result
    // 下载
    window.location.href = url
  }
  // 开始读取指定的Blob中的内容。一旦完成，result属性中将包含一个data: URL格式的Base64字符串以表示所读取文件的内容。
  reader.readAsDataURL(res)
}
```
注意： 使用 FileReader对象封装二进制流之后没办法设置下载的文件名，默认文件名为下载，文件格式根据读取的是请求返回的content-type。

##### 方法四、使用插件file-saver
下载插件
```
 npm install --save file-saver
```
引入插件
```
 import { saveAs } from 'file-saver'
```
封装下载方法，使用插件
```
 export const downloadFile = (res, type, filename) => {
   // 将二进制数据封装成blob对象
    const blob = new Blob([res], {
     type: type
   })
   /**
     这里也可以使用File对象封装二进制数据
     const file = new File([res], filename, {
       type: type
     });
   */
   // 调用插件方法
   saveAs(blob,filename)
 }
```
[插件源码](https://github.com/eligrey/FileSaver.js)
##### 方法五、使用插件downloadjs
下载插件
```
 npm install --save downloadjs
```
引入插件
```
 const download = require('downloadjs')
```
因为downloadjs源码中没有通过export导出download方法，而是通过return的方式返回download方法，因为需要使用require来引入文件。

封装下载方法，使用插件
```
  export const downloadFile = (res, type, filename) => {
    // 将二进制流转成blob对象
    const blob = new Blob([res], {
      type: type
    })
    // 调用插件方法
    download(blob, filename, type);
  }

```
[插件源码](https://github.com/rndme/download)
##### 二、发送请求，获取二进制流数据
```
// index.vue
<template>
  <div>
    <button @click='handleDownload'>下载</button>
  </div>
</template>
<script>
// 引入下载方法
import {download} from 'utils'
export default{
  methods: {
    async downloadFile () {
      let res = await axios.get(
        url: 'xxxx/xxxx',
        method: 'GET',
        // 设置返回数据类型，这里一定要设置，否则下载下来的文件会是空白,也可以是`arraybuffer`
        responseType: 'blob',
        params: {
          id: 'xxxxxx'
        }
      )
      // 获取在response headers中返回的下载文件类型
      let type = JSON.parse(res.headers)['content-type']
      
      /*获取在response headers中返回的下载文件名
        因为返回文件名是通过encodeURIComponent()函数进行了编码，因此需要通过decodeURIComponent()函数解码
      */
      let fileName = decodeURIComponent(JSON.parse(res.headers)['file-name'])
      // 调用封装好的下载函数
      download(res, type, fileName)
    },
    // 点击下载按钮下载
    handleDownload() {
      this.downloadFile()
    }
  }
}
</script>
```
##### 通过数据源拼接生产excel文件
```
const outputExcel = () => {
        let resultContent = 'A, B, C, D\n'
        let transferContent = resultContent => {
            //根据数据 利用a标签模仿下载生成本地文件
            let date = moment(new Date()).format('YYYY-MM-DD')
            const blob = new Blob(['\ufeff', resultContent], {
                type: 'text/csv', // application/x-xls text/csv
            })
            const objectURL = window.URL.createObjectURL(blob)
            const download = document.createElement('a')
            download.href = objectURL
            download.download = `${date}_${'文件名'}.${'csv'}`
            download.click()
            message.success('文件已下载')
        }
        let generateTxt = message => {
            var element = document.createElement('a')
            element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(message))
            element.setAttribute('download', '文件名' + new Date().getTime())
            element.style.display = 'none'
            document.body.appendChild(element)
            element.click()
            document.body.removeChild(element)
        }
        let current = 1,
            pageSize = 8000

        Modal.confirm({
            title: '提示',
            content: '确认导出全部信息吗？',
            okText: '确认',
            cancelText: '取消',
            onOk: () => {
                // 一次访问8000条数据的方案
                refetch({
                    filter: {},
                    pager: { sort: '', current, pageSize },
                    roleId,
                }).then(values => {
                    console.log('values', values)
                    const total = values.data.allRoleUsers.total
                    const nodes = values.data.allRoleUsers.nodes
                    if (total > pageSize) {
                        message.warning('用户数量太多')
                    } else {
                        for (let node of nodes) {
                            let expireDate = moment(node['expireDate']).format('YYYY-MM-DD')
                            if (expireDate === '9999-12-31') {
                                expireDate = '永久有效'
                            }
                            resultContent +=
                                node['A'] +
                                ',' +
                                node['B']['C'] +
                                ',' +
                                expireDate +
                                ',' +
                                moment(node['D']).format('YYYY-MM-DD') +
                                '\n'
                        }
                        // transferContent(resultContent)
                        generateTxt(resultContent)
                    }
                })
                // 轮询接口的方案
                // const timer = setInterval(() => {
                //     // console.log('current', current, Math.ceil(total / pageSize) + 1)
                //     if (current < Math.ceil(total / pageSize) + 1) {
                //         fetchMore({
                //             variables: {
                //                 filter: {},
                //                 pager: { sort: '', current, pageSize },
                //                 roleId,
                //             },
                //             updateQuery: (previousResult, { fetchMoreResult }) => {
                //                 // console.log('previousResult', previousResult, fetchMoreResult)
                //                 const nodes = fetchMoreResult?.allRoleUsers?.nodes
                //                 for (let node of nodes) {
                //                     resultContent +=
                //                         node['A'] +
                //                         ',' +
                //                         node['B']['C'] +
                //                         ',' +
                //                         moment(node['D']).format('YYYY-MM-DD') +
                //                         '\n'
                //                 }
                //             },
                //         })
                //         current++
                //     } else {
                //         // 校验下载量是否与total值一致，一致则下载，反之则给出提示
                //         const fileDateLines = resultContent.split('\n').length - 1
                //         if (fileDateLines === total + 1) {
                //             transferContent(resultContent)
                //         } else {
                //             message.warning('数据量较大，导出数据出错，请联系管理员')
                //         }
                //         clearInterval(timer)
                //     }
                // }, 500)
            },
        })
    }
```