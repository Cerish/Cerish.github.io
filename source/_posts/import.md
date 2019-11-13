---
title: 前端如何导入文件
date: 2019-10-08 12:26:11
tags:
- vue
- js
---

# 1 配合 xlsx 倒入 Excel 文件
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



