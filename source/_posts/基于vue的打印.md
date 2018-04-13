---
title: 基于vue的打印
date: 2018-04-12 16:46:49
tags:  [vue,canvas,npm,javascript]
categories: vue
---

业务需求：目前开发的财务ERP系统，跟金蝶友商类似。科目打印需自动拼接上辅助核算，如科目有外币需打印外币
<!-- more -->
<img src="https://ws1.sinaimg.cn/large/694830ebgy1fh3pyd05klj21kw0f2dy2.jpg"  title="记账凭证" />
插图：先用需要打印的页面用canvas画出 传成pdf 之后后台配合预览打印pdf。
步骤一：安装2个依赖 npm install jspdf html2canvas -D

步骤二：基于jspdf与html2canvas封装一个全局打印方法

代码：

import html2Canvas from 'html2canvas'

import JsPDF from 'jspdf'

import axios from 'axios' //把pdf传给后台

import qs from 'qs'//需要转换 用JOSN.stringify()不行

export default {

  install(Vue, options) {

Vue.prototype.getPdf= function (dom) {
      // var title = this.htmlTitle

      html2Canvas(document.querySelector(dom), {

        allowTaint: true

      }).then(function (canvas) {

        let contentWidth = canvas.width

        let contentHeight = canvas.height

        //一页pdf显示html页面生成的canvas高度;

        let pageHeight = contentWidth / 595.28 * 841.89

        //未生成pdf的html页面高度

        let leftHeight = contentHeight

        //页面偏移

        let position = 0

        //a4纸的尺寸[595.28,841.89]，html页面生成的canvas在pdf中图片的宽高

        let imgWidth = 595.28

        let imgHeight = 592.28 / contentWidth * contentHeight

        let pageData = canvas.toDataURL('image/jpeg', 1.0)

        // 三个参数，第一个方向，第二个尺寸，第三个尺寸格式

        let PDF = new JsPDF('', 'pt', 'a4')

        //有两个高度需要区分，一个是html页面的实际高度，和生成pdf的页面高度(841.89)

        //当内容未超过pdf一页显示的范围，无需分页

        if (leftHeight < pageHeight) {

          PDF.addImage(pageData, 'JPEG', 0, 0, imgWidth, imgHeight)

        } else {

          while (leftHeight > 0) {

            PDF.addImage(pageData, 'JPEG', 0, position, imgWidth, imgHeight)

            leftHeight -= pageHeight

            position -= 841.89

            //避免添加空白页

            if (leftHeight > 0) {

              PDF.addPage()

            }

          }

        }

        var instance = axios.create({

          timeout: 10000,

headers: {'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'}

        });



instance.post(axios.defaults.baseURL+'/file/savepdf',qs.stringify({

//这个有毒，output输出文件流，PDF.output('datauristring')输出base64文件流。
file:PDF.output('datauristring')//base64的文件流

        }))

        .then(function (response) {

window.open(axios.defaults.baseURL+'/file/showpdf/'+response.data.data.uri)//在新的窗口打开

          // let dom=document.createElement('a');

          // dom.setAttribute('href',axios.defaults.baseURL+'/file/showpdf/'+response.data.data.uri);

          // dom.setAttribute('target','_blank');

         // dom.click();//模拟新的窗口打开

        })

        .catch(function (error) {

          console.log(error);

        });

      })

    }

  }

}

步骤三：调用

  //打印凭证

    printVoucher() {

      //日期

      let Voucherdate = new Date(this.pickerOptionsValue);

      let tableData = this.childData; //整个视图数据

      let trData = this.childData.trData; //列数据

      //拼凑数据供打印使用,凭证头，尾信息

      this.voucherHFdata = {

        voucherTitle: "记账凭证", //记账凭证

        billNum: this.billNum, //附件数

        accbookName: this.$store.state.accbookName, //账套名

        date:

          Voucherdate.getFullYear() +

          "-" +

          (Voucherdate.getMonth() + 1) +

          "-" +

          Voucherdate.getDate(), //日期

        voucherNumber: this.voucherName + "-" + this.voucherNumberValue, //凭证号：大-16

        lotal: tableData.DeCr_Total, //合计

        supervisor: "", //主管

        cashier: "", //出纳

        auditor: "", //审核

        producer: this.$store.state.voucherProducer //制单

      };

      //需打印的列数据过滤

      // let listData = [];

      trData.forEach((n, i) => {

        //科目是否有辅助核算，有辅助核算，拼接上辅助核算eg:1001 库存现金_销售部

        let subjectVal =

          n.subject.val +

          (n.subject.auxData.length > 0 ? "_" : "") +

          n.subject.auxData

            .map(item1 => {

              return (

                item1.options.filter(item2 => {

                  if (item1.value === item2.uid) return item2;

                })[0].name || ""

              );

            })

            .join("_");

        //是否有外币，没有外币显示空。有外币匹配过滤出外币名称eg：RMB

        let curName =

          n.currency.currencyOptions

            .filter(item => {

              if (item.value === n.currency.currencyValue) return item;

            })

            .map(item => {

              return item.label;

            })[0] || "";

        this.listData[i] = {

          abstract: n.abstract.val, //摘要

          subject: subjectVal, //科目是否有辅助核算，有辅助核算，拼接上辅助核算

          currencyName: curName, //是否有外币，没有外币显示空。有外币匹配过滤出外币名称eg：RMB

          showCur:n.currency.show,//false不显示

          exchangeRate: n.currency.exchangeRate, //汇率

          original: n.currency.original, //原币

          deVal: n.DeCr.De_val, //借方金额

          crVal: n.DeCr.Cr_val//贷方金额

        };

      });

this.$refs.print.printvoucher(); //打印
    },



   



模板：

export default {

  name: "printVoucher",

  props: ["voucherHFdata", "listData"],

  data() {

    return {

      // htmlTitle: "voucher"

      tableData:[],

      hascur:false,

    };

  },

  mounted() {},

  methods: {

    printvoucher() {

      this.voucher5tr(

        setTimeout(()=>{

          this.getPdf("#printVoucher");

        },1000)

      );

    },

    //每5列切成一张凭证

voucher5tr() {

      let tr5 = []; //[[{},{},{},{},{}]]一维变多维

      let tr = this.listData;

      let index = 0;

      tr.forEach((n, i) => {

        if (!tr5[index]) {

          tr5[index] = [];

        }

        tr5[index].push(n);

        if (tr5[index].length === 5) {

          index++;

        }

      });

      let last = tr5[tr5.length - 1];

      for (let i = 0,l = 5 - last.length; i < l; i++) {

        last.push({

          abstract: "", //摘要

          showCur:false,//外币不显示

          subject: "", //科目是否有辅助核算，有辅助核算，拼接上辅助核算

          currencyName: "", //是否有外币，没有外币显示空。有外币匹配过滤出外币名称eg：RMB

          exchangeRate: "", //汇率

          original: "", //原币

          crVal: "", //贷方金额

          deVal: "" //借方金额

        });

      }

      this.tableData=tr5;

      this.tableData.forEach(n=>{

        n.forEach(n1=>{

          if(n1.showCur===true){

            this.hascur=true;

          }

        })

      })

    }

  }

};

作者：清减半夏时光
链接：https://www.jianshu.com/p/3665fbc2533b
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。