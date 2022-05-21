# H5打开Excel调研



经过前期调研，目前主要集中在两个sdk都可以打开Excel。在选型上差别很大，这里比较记录下。



1. **常规方法**

   一般来讲，前端是读不到文件的，需要后端读出Excel文件，并将数据传递给前端并渲染出来，那如何描述excel文件内容及格式就成了关键。

   http://10.66.14.10:1111/json-excel.html

   <img src="/Users/ludejun/Library/Application Support/typora-user-images/image-20220409202830023.png" alt="image-20220409202830023" style="zoom:50%;" />

   非常像真实Excel文件，H5打开初始为真实大小，可以左右拖拉无需放大缩小。需要的数据格式比较复杂，需要后端读取excel后返回一个json文件。示例如下：

   ```json
   [
         {
           name: '推进中...', // 第一个sheet名字
           styles: [ 
             {
               bgcolor: '#3575cd',
               textwrap: true,
               align: 'center',
               color: '#fff',
               border: {
                 top: ['thin', '#0366d6'],
                 bottom: ['thin', '#0366d6'],
                 right: ['thin', '#0366d6'],
                 left: ['thin', '#0366d6'],
               },
             },
             {
               bgcolor: '#D0021B',
               textwrap: true,
               color: '#333',
               border: {
                 top: ['thin', '#000'],
                 bottom: ['thin', '#000'],
                 right: ['thin', '#000'],
                 left: ['thin', '#000'],
               },
             },
           ], // 所有使用到的样式索引，之后在rows中直接使用 style: [index]
           merges: [
             'A2:K2',
           ], // 真实所有合并的单元格，每一个都是一个向量
           cols: {
             len: 12, // 展示列数
             3: { width: 250 }, // 规定列宽
             5: { width: 250 }, // 规定列宽
           },
           rows: {
             len: 80, // 展示所有行数，包括空行
             0: {
               cells: {
                 0: { text: '提出日期', style: 0 }, // style表示使用哪个index的样式
                 1: { text: '分类', style: 0 },
                 2: { text: '技术线', style: 0 },
                 3: { text: '技术点', style: 0 },
                 4: { text: '总进度', style: 0 },
                 5: { text: '进展', style: 0 },
                 6: { text: '负责人', style: 0 },
                 7: { text: '预期解决日期', style: 0 },
                 8: { text: '是否已上线', style: 0 },
                 9: { text: '优先级', style: 0 },
                 10: { text: '备注信息', style: 0 },
               },
             },
             1: {
               cells: {
                 0: { text: '前端技术框架', style: 1, merge: [0, 10] }, // merge表示样式上看起来合起来的 [高-1 , 宽-1]
               }
             },
             2: {
               cells: {
                 0: { text: '2022/3/22'},
                 1: { text: '整体'},
                 2: { text: 'TS推广'},
                 3: { text: '1. RN项目TS配置、TS提升'},
                 4: { text: '100%', style: 1 },
                 5: { text: '解决RN项目ts报错问题全部修复'},
                 6: { text: 'coco'},
                 7: { text: '2022.4'},
                 8: { text: '' },
                 9: { text: 'P1' },
                 10: { text: ''},
               }
             },
             3: {
               cells: {
                 0: { text: '2022/3/22'},
                 1: { text: '整体'},
                 2: { text: 'TS推广'},
                 3: { text: '1. RN项目TS配置、TS提升'},
                 4: { text: '100%', style: 1 },
                 5: { text: '解决RN项目ts报错问题全部修复'},
                 6: { text: 'coco'},
                 7: { text: '2022.4'},
                 8: { text: '' },
                 9: { text: 'P1' },
                 10: { text: ''},
               }
             }
           }
         }, {
           name: '待启动', // 第二个sheet的name
         }
       ]
   ```

   

2. **直接打开Excel文件**

   这种方法比较直接，可以直接打开excel文件，流文件不太确定。打开文件渲染出来基础的excel还比较像，外面的框子和第一个差一点。H5打开比较小，需要手动放大。看起来有优势，但是资源文件大很多，会比较慢。

   http://10.66.14.10:1111/open-xlsx-file.html

   <img src="/Users/ludejun/Library/Application Support/typora-user-images/image-20220409202843413.png" alt="image-20220409202843413" style="zoom:50%;" />

   问题：不支持改变字体大小（文字都一样大），艺术字、文本框、chart、图形不支持，符号、表情支持。

   原因：解析excel文件使用的是Sheetjs免费版，收费版Sheetjs Pro支持这些。https://sheetjs.com/pro


### 详细比较

|              | Json-excel                                                   | open-xlsx-file                                               |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 开发难度     | 难，需要后端读取excel并生成符合一定格式的json，不知道目前读出来的是什么形式，我估计这个json样子设计的和后端读出来的可以兼容？ | ✅直接打开excel文件                                           |
| 安全性       | ✅本质上就是获取json，和正常接口一样                          | 差，现在只支持excel URL，流接口不知道能否渲染，就算支持流，也需要通过GET Url能读到，参数也都在url上 |
| 静态资源大小 | ✅453K                                                        | 2.4M，加载肯定会慢点                                         |
| 流行度       | ✅支持编辑，渲染更像excel，主要是PC上功能强大                 | 也可以编辑，H5上和第一个也不差多少                           |
| 其他体验     | 优先打开第一个sheet，比较好点                                | 优先打开最后一个sheet，第一次打开的时候tab渲染样式上感觉稍微有点问题不好复现 |



**结论：如果后端读出来的excel格式和需要的json差不多就用第一种方案，如果有较大差异就直接用第二种，想办法解决其他问题。**



参考文件

open excel start: https://jspreadsheet.com/v8/docs/getting-started

open excel demo: https://bossanova.uk/jspreadsheet/v4/examples/import-data

第二个sdk的在线普通json例子：https://codepen.io/hchiam/pen/qBRzXKK

第一个sdk在线例子（json见源码）：https://myliang.github.io/x-spreadsheet/

第一个sdk api：https://hondrytravis.com/x-spreadsheet-doc/doc/style.html#border-%E8%BE%B9%E6%A1%86%E6%A0%B7%E5%BC%8F

第一个sdk github：https://github.com/myliang/x-spreadsheet