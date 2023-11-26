<template>
  <div class="col">
    包名：
    <input type="text" :value="name" class="input">
  </div>
  <div class="file-input" id="drop-zone"
       v-bind:class="{ 'drag-over': isDragOver }"
       @dragover.prevent="handleDragOver"
       @dragleave="handleDragLeave"
       @drop.prevent="handleDrop"
  >
    将打包的文件拖拽到此处（拖拽文件夹无效）
  </div>
</template>



<script setup lang="ts">
import { ref } from 'vue'
import JSZip from 'jszip';
import { saveAs } from 'file-saver';

defineProps<{ msg: string }>()
let name:any = ref('images')
let isDragOver: any = ref(false);
const handleDragOver = (e:any)=>{
  // console.log(e)
}
const handleDragLeave = (e:any)=>{
  // console.log(e)
}
const files2:any = []
const handleDrop = (event:any)=>{
  // 放下
  event.preventDefault();
  isDragOver = false;

  const files = event.dataTransfer.files;

  for (let i = 0; i < files.length; i++) {
    const file = files[i];
    files2.push(file)
  }

  const zip = new JSZip();

  files2.forEach((file:any) => {
    console.log("ss", file)
    const filename = file.path.substring(file.path.lastIndexOf('/') + 1);
    zip.file(filename, file);
  });
  const htmlFile:File = new File([generateHTMLFile()], `${name.value}.html`)
  zip.file(`/${name.value}.html`, htmlFile)
  console.log(zip)

  // 生成zip文件
  zip.generateAsync({ type: 'blob' }).then((content) => {
    saveAs(content, `${name.value}.zip`);
  });
}
const generateHTMLFile = () => {
  let imgHtml = ""
  files2.forEach((t:any) => {
    imgHtml+= `<img src="${t.name}" />`
  })

  // HTML内容
  const htmlContent = `
        <!DOCTYPE html>
        <html>
        <head>
          <title>My HTML File</title>
        </head>
        <body>
            ${imgHtml}
        </body>
        </html>
      `;

  // 创建Blob对象
  const blob:Blob = new Blob([htmlContent], { type: 'text/html' });
  return blob
}
</script>


<style scoped>
.file-input{
  width: 100%;
  height: 150px;
  text-align: center;
  line-height: 150px;
  margin: 20px auto 0;
  background: #eeeeee;
  color: #666666;
}
.col{
  margin-top: 0px;
}
</style>
