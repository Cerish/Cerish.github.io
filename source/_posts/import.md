---
title: 前端如何导入导出文件
date: 2019-10-08 12:26:11
tags:
- vue
- js
---
# 导入文件部分

## 1 配合 xlsx 导入 Excel 文件，并获取表格信息
html如下，(由于input样式单调，可以通过其他样式，只需要改变点击指向即可)

    <input type="file"
      ref="fileInput" 
      accept=".xlsx, .xls" // 设置接受文件类型
      multiple  // 多个文件一起上传
      @change="handleClick">
    <div>
      <button @click="$refs.fileInput.click()">点击</button>
    </div>

js部分
    // 获取上传的文件

    handleClick(e) {
      const files = e.target.files; // 上传文件的列表
      const rawFile = files[0]; // 获取最近上传的一个(第一个)
      this.uploadFile(rawFile);
    },
    // 解决文件不可重复上传，不需要可直接跳过
    uploadFile(file) {
      this.$refs['fileInput'].value = null // 解决文件不可重复上传的问题
      this.readerData(file);
    },
    getHeaderRow(sheet) {
      const headers = []
      const range = XLSX.utils.decode_range(sheet['!ref'])
      let C
      const R = range.s.r
      /* start in the first row */
      for (C = range.s.c; C <= range.e.c; ++C) { // 查询第一行的信息
        const cell = sheet[XLSX.utils.encode_cell({ c: C, r: R })]
        /* find the cell in the first row */
        let hdr = 'UNKNOWN ' + C // <-- replace with your desired default
        if (cell && cell.t) hdr = XLSX.utils.format_cell(cell)
        headers.push(hdr)
      }
      return headers
    },
    readerData(rawFile) {
      const reader = new FileReader(); // 生成一个读取文件的对象
      reader.onload = (e) => {  // 文件读取成功的操作
        const data = e.target.result // 获取文件结果
        const workbook = XLSX.read(data, {type: 'array'}) // 工作簿以数组形式读取，记得先引入xlsx
        const firstSheetName = workbook.SheetNames[0] // 获取文件名称
        const worksheet = workbook.Sheets[firstSheetName] // 获取文件名称，获取工作表
        const header = this.getHeaderRow(worksheet) // 获取Excel头部信息
        const results = XLSX.utils.sheet_to_json(worksheet) // 获取excel表格信息
        const meta = { sheetName: firstSheetName } // 设置表格meta信息
      }
      reader.readAsArrayBuffer(rawFile); // 读取传入的文件
    }

到此，header 是表头信息，results是以表头信息为属性的json数组。

## 其他文件导入
同样利用 type='file' 进行文件传入，然后利用 fromdata 进行文件发送给服务器，预览显示利用ifarme进行src请求。txt纯文本文件可以通过new FileReader（） 进行访问获取信息，但是其他文件会乱码，例如.doc, .docx。

# 导出文件

## 导出 Excel表格文件

导出时，首先下载两个插件

    npm i script-loader
    npm i file-saver

再引入excel.js

    /* eslint-disable */
    require('script-loader!file-saver');
    import XLSX from 'xlsx'

    function generateArray(table) {
      var out = [];
      var rows = table.querySelectorAll('tr');
      var ranges = [];
      for (var R = 0; R < rows.length; ++R) {
        var outRow = [];
        var row = rows[R];
        var columns = row.querySelectorAll('td');
        for (var C = 0; C < columns.length; ++C) {
          var cell = columns[C];
          var colspan = cell.getAttribute('colspan');
          var rowspan = cell.getAttribute('rowspan');
          var cellValue = cell.innerText;
          if (cellValue !== "" && cellValue == +cellValue) cellValue = +cellValue;

          //Skip ranges
          ranges.forEach(function (range) {
            if (R >= range.s.r && R <= range.e.r && outRow.length >= range.s.c && outRow.length <= range.e.c) {
              for (var i = 0; i <= range.e.c - range.s.c; ++i) outRow.push(null);
            }
          });

          //Handle Row Span
          if (rowspan || colspan) {
            rowspan = rowspan || 1;
            colspan = colspan || 1;
            ranges.push({
              s: {
                r: R,
                c: outRow.length
              },
              e: {
                r: R + rowspan - 1,
                c: outRow.length + colspan - 1
              }
            });
          };

          //Handle Value
          outRow.push(cellValue !== "" ? cellValue : null);

          //Handle Colspan
          if (colspan)
            for (var k = 0; k < colspan - 1; ++k) outRow.push(null);
        }
        out.push(outRow);
      }
      return [out, ranges];
    };

    function datenum(v, date1904) {
      if (date1904) v += 1462;
      var epoch = Date.parse(v);
      return (epoch - new Date(Date.UTC(1899, 11, 30))) / (24 * 60 * 60 * 1000);
    }

    function sheet_from_array_of_arrays(data, opts) {
      var ws = {};
      var range = {
        s: {
          c: 10000000,
          r: 10000000
        },
        e: {
          c: 0,
          r: 0
        }
      };
      for (var R = 0; R != data.length; ++R) {
        for (var C = 0; C != data[R].length; ++C) {
          if (range.s.r > R) range.s.r = R;
          if (range.s.c > C) range.s.c = C;
          if (range.e.r < R) range.e.r = R;
          if (range.e.c < C) range.e.c = C;
          var cell = {
            v: data[R][C]
          };
          if (cell.v == null) continue;
          var cell_ref = XLSX.utils.encode_cell({
            c: C,
            r: R
          });

          if (typeof cell.v === 'number') cell.t = 'n';
          else if (typeof cell.v === 'boolean') cell.t = 'b';
          else if (cell.v instanceof Date) {
            cell.t = 'n';
            cell.z = XLSX.SSF._table[14];
            cell.v = datenum(cell.v);
          } else cell.t = 's';

          ws[cell_ref] = cell;
        }
      }
      if (range.s.c < 10000000) ws['!ref'] = XLSX.utils.encode_range(range);
      return ws;
    }

    function Workbook() {
      if (!(this instanceof Workbook)) return new Workbook();
      this.SheetNames = [];
      this.Sheets = {};
    }

    function s2ab(s) {
      var buf = new ArrayBuffer(s.length);
      var view = new Uint8Array(buf);
      for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xFF;
      return buf;
    }

    export function export_table_to_excel(id) {
      var theTable = document.getElementById(id);
      var oo = generateArray(theTable);
      var ranges = oo[1];

      /* original data */
      var data = oo[0];
      var ws_name = "SheetJS";

      var wb = new Workbook(),
        ws = sheet_from_array_of_arrays(data);

      /* add ranges to worksheet */
      // ws['!cols'] = ['apple', 'banan'];
      ws['!merges'] = ranges;

      /* add worksheet to workbook */
      wb.SheetNames.push(ws_name);
      wb.Sheets[ws_name] = ws;

      var wbout = XLSX.write(wb, {
        bookType: 'xlsx',
        bookSST: false,
        type: 'binary'
      });

      saveAs(new Blob([s2ab(wbout)], {
        type: "application/octet-stream"
      }), "test.xlsx")
    }

    export function export_json_to_excel({
      multiHeader = [],
      header,
      data,
      filename,
      merges = [],
      autoWidth = true,
      bookType=  'xlsx'
    } = {}) {
      /* original data */
      filename = filename || 'excel-list'
      data = [...data]
      data.unshift(header);

      for (let i = multiHeader.length-1; i > -1; i--) {
        data.unshift(multiHeader[i])
      }

      var ws_name = "SheetJS";
      var wb = new Workbook(),
        ws = sheet_from_array_of_arrays(data);

      if (merges.length > 0) {
        if (!ws['!merges']) ws['!merges'] = [];
        merges.forEach(item => {
          ws['!merges'].push(XLSX.utils.decode_range(item))
        })
      }

      if (autoWidth) {
        /*设置worksheet每列的最大宽度*/
        const colWidth = data.map(row => row.map(val => {
          /*先判断是否为null/undefined*/
          if (val == null) {
            return {
              'wch': 10
            };
          }
          /*再判断是否为中文*/
          else if (val.toString().charCodeAt(0) > 255) {
            return {
              'wch': val.toString().length * 2
            };
          } else {
            return {
              'wch': val.toString().length
            };
          }
        }))
        /*以第一行为初始值*/
        let result = colWidth[0];
        for (let i = 1; i < colWidth.length; i++) {
          for (let j = 0; j < colWidth[i].length; j++) {
            if (result[j]['wch'] < colWidth[i][j]['wch']) {
              result[j]['wch'] = colWidth[i][j]['wch'];
            }
          }
        }
        ws['!cols'] = result;
      }

      /* add worksheet to workbook */
      wb.SheetNames.push(ws_name);
      wb.Sheets[ws_name] = ws;

      var wbout = XLSX.write(wb, {
        bookType: bookType,
        bookSST: false,
        type: 'binary'
      });
      saveAs(new Blob([s2ab(wbout)], {
        type: "application/octet-stream"
      }), `${filename}.${bookType}`);
    }

使用如下，一般使用 export_json_to_excel 这个方法进行导出

    import('@/vendor/Export2Excel').then(excel => {
        const list = this.users
        const data = this.formatJson(this.headerVal, list)
        console.log(data)
        excel.export_json_to_excel({
          header: this.headerTitle,  //表头数据
          data,  // 行数据
          filename: this.fileName,  // 导出文件名称
          autoWidth: this.cellAutoWidth,  // 自动宽度？
          bookType: this.selectedFormat  // 导出类型（xlsx等）
        })
      })
    // 表格行数据的过滤属性获取
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => {

        return v[j]
      }))
    },

至此，Excel表格数据导出结束。

## 其他文件导出

学会并实践再更
