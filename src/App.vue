<template>
  <div>
    <input id="file" type="file" />
  </div>
</template>

<script>
  export default {
    mounted() {
      file.addEventListener('change', async (e) => {
        const file = e.target.files[0]
        const flag = await this.isImage(file)
        if (flag) {
          alert('上传格式通过！')
        } else {
          alert('请上传正确的格式！')
        }
      })
    },
    methods: {
      // 判断是否为图片
      async isImage(file) {
        // 三种格式判断同时进行
        const arr = await Promise.all([this.isJpg(file), this.isPng(file), this.isGif(file)])
        // 有一个为 true，则返回 true
        return arr.some(item => item)
      },
      // 判断是否为 jpg 格式
      async isJpg(file) {
        const res = await this.blobToString(file.slice(0, 3))
        return res === 'FF D8 FF'
      },
      // 判断是否为 png 格式
      async isPng(file) {
        const res = await this.blobToString(file.slice(0, 4))
        return res === '89 50 4E 47'
      },
      // 判断是否为 gif 格式
      async isGif(file) {
        const res = await this.blobToString(file.slice(0, 4))
        return res === '47 49 46 38'
      },
      // 将文件转为十六进制字符串
      async blobToString(blob) {
        return new Promise(resolve => {
          const reader = new FileReader()
          reader.onload = function () {
            const res = reader.result
              .split('') // 将读取结果分割为数组
              .map(v => v.charCodeAt()) // 转为 Unicode 编码
              .map(v => v.toString(16).toUpperCase()) // 转为十六进制，再转大写
              .map(v => v.padStart(2, '0')) // 个位数补0
              .join(' ') // 转为字符串
            resolve(res)
          }
          reader.readAsBinaryString(blob) // 将文件读取为二进制字符串
        })
      }
    }
  }
</script>

<style lang="scss" scoped></style>
