/*
 * @Author: 唐聪
 * @Date: 2018-09-20 17:17:01
 * @Last Modified by: 唐聪
 * @Last Modified time: 2018-09-20 17:17:38
 */
<template>
  <div class="posterEditor">
    <img id="demo" alt="" style="width:auto;max-height:100px">
    <el-button @click="dialogVisible = true,nowStep = 1">海报</el-button>
    <el-dialog
      :visible.sync="dialogVisible"
      width="800px"
      :title="title">
      <div class="poster-content">
        <el-scrollbar style="height:100%">
          <!-- 海报 -->
          <div ref="firstStep" v-if="nowStep == 1 || nowStep == 3 || nowStep == 6" class=" el-scrollbar" @click="turnToActive($event)" @dblclick="turnToActiveDbl($event)">
              <p>320x400竖版海报尺寸 <i class="icon-pencil icon"></i> </p>
              <div class="imgDiv">
                <div class="img-wrap">
                  <img src="../../assets/example.jpg" alt="">
                </div>
                <span>320x400</span>
              </div>
              <div class="imgDiv">
                <div class="img-wrap">
                  <img src="../../assets/logo.png" alt="">
                </div>
                <span>320x400</span>
              </div>
              <div class="imgDiv">
                <div class="img-wrap">
                  <img src="../../assets/logo.png" alt="">
                </div>
                <span>320x400</span>
              </div>
              <p>500x280竖版海报尺寸 <i class="icon-pencil icon"></i></p>
              <div class="imgDiv">
                <div class="img-wrap">
                  <img src="../../assets/example.jpg" alt="">
                </div>
                <span>500x280</span>
              </div>
              <p>1280x720 <i class="icon-pencil icon"></i></p>
              <div class="imgDiv">
                <div class="img-wrap">
                  <img src="../../assets/logo.png" alt="">
                </div>
                <span>1280x720</span>
              </div>
          </div>
          <!-- 本地上传-上传图片 -->
          <div v-if="nowStep == 2">
            <span class="alert">批量上传前，请检查图片是否已按照规则命好名，如‘500x280’</span>
            <el-upload
              ref="upload"
              multiple
              action= ''
              accept='image/*'
              class="el-upload2"
              list-type="picture-card"
              :on-remove="handleRemove"
              :on-change="change"
              :auto-upload="false">
              <i id="uploadI" class="el-icon-plus" @click="demo" style="padding:40%"></i>
            </el-upload>
          </div>
          <!-- 选择尺寸（图片裁剪界面） -->
          <div v-if="nowStep == 4" class="choose-size" ref="forthStep">
            <!-- 海报预览 -->
            <div class="left-preview">
              <p class="p-header">海报预览</p>
              <div class="preview-content-wrap">
                <el-scrollbar style="height:100%">
                <div class="preview-content" @click="showToEditor($event)">
                  <el-collapse v-model="activeNames">
                    <el-collapse-item title="320x400横版海报" name="1">
                      <div class="img-wrap">
                        <img src="../../assets/logo.png" >
                      </div>
                      <div class="img-wrap">
                        <img src="../../assets/example.jpg" >
                      </div>
                      <div class="img-wrap">
                        <img src="../../assets/d1.gif" >
                      </div>
                    </el-collapse-item>
                    <el-collapse-item title="250x400横版海报" name="2">
                      <div class="img-wrap">
                        <img src="../../assets/e1.png" >
                      </div>
                      <div class="img-wrap">
                        <img src="../../assets/e2.png" >
                      </div>
                    </el-collapse-item>
                    <el-collapse-item title="420x300横版海报" name="3">
                      <div class="img-wrap">
                        <img src="../../assets/e3.jpg" >
                      </div>
                      <div class="img-wrap">
                        <img src="../../assets/e3.jpg" >
                      </div>
                    </el-collapse-item>
                    <el-collapse-item title="1320x900横版海报" name="4">
                      <div>用户决策：根据场景可给予用户操作建议或安全提示，但不能代替用户进行决策；</div>
                      <div>结果可控：用户可以自由的进行操作，包括撤销、回退和终止当前操作等。</div>
                    </el-collapse-item>
                  </el-collapse>
                </div>
                </el-scrollbar>
              </div>
            </div>
            <!-- 图片编辑器区域 -->
            <div class="center-editor" ref="editor">
              <canvas class="canvas1" ref="canvas1" width="400" height="400"></canvas>
              <div class="photo-clip-mask"
                v-if="editorImg"
                @mousedown="onMoveStart"
                @mousemove="onMove"
                @mouseup="onChangeEnd"
                @mouseleave="onChangeEnd">
                <div class="photo-clip-mask-left"
                  :style="{'height': maskL.height+'px','top': maskL.top+'px','width': maskL.width+'px'}"
                ></div>
                <div class="photo-clip-mask-right"
                  :style="{'height':maskR.height+'px','left':maskR.left+'px','top':maskR.top+'px'}"
                ></div>
                <div class="photo-clip-mask-top"
                  :style="{'height': maskT.height+'px'}"
                ></div>
                <div class="photo-clip-mask-bottom"
                  :style="{'height':maskB.height+'px','top':maskB.top+'px'}"
                ></div>
                <div class="photo-clip-area"
                  :style="{'width':clip.width+'px','height':clip.height+'px','left':clip.left+'px','top':clip.top+'px'}"
                ></div>
                <span class="photo-clip-area-bottom-right" ref="br"
                  :style="{'bottom': br.bottom + 'px','margin-left':br.marginLeft + 'px'}"></span>
                <span class="photo-clip-area-bottom-left" ref="bl"
                  :style="{'bottom': bl.bottom + 'px','margin-left':bl.marginLeft + 'px'}"></span>
                <span class="photo-clip-area-top-left" ref="tl"
                  :style="{'top': tl.top + 'px','margin-left':tl.marginLeft + 'px'}"></span>
                <span class="photo-clip-area-top-right" ref="tr"
                  :style="{'top': tr.top + 'px','margin-left':tr.marginLeft + 'px'}"></span>
              </div>
              <div v-else class="blank-photo-clip-mask">
                <div class="contener-wrap">
                  <div class="reUploadSmall" @click="reUploadSmall">
                    <i class="icon icon-reUploadSmall"></i>
                    <span>本地上传</span>
                    <input type="file" ref="reUploadSmallInput">
                  </div>
                  <div class="capture" @click="nowStep = 5">
                    <i class="icon icon-reUploadSmall"></i>
                    <span>截取帧画</span>
                  </div>
                </div>
              </div>
            </div>
            <div class="center-handle">
              <div class="center-handle-centent" v-show="editorImg">
                <i class="center-icon reUpload"></i>
                <i class="center-icon save" @click="savePhotoClip"></i>
              </div>
            </div>
            <!-- 选择设配尺寸区域 -->
            <div class="right-handle">
              <p class="p-header">设配尺寸</p>
                <el-checkbox-group v-model="sizecheckList">
                  <el-checkbox label="250x250"></el-checkbox>
                  <el-checkbox label="652x260"></el-checkbox>
                  <el-checkbox label="1280x980"></el-checkbox>
                </el-checkbox-group>
            </div>
          </div>
        </el-scrollbar>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button v-show="nowStep == 1" type="primary" @click="nowStep = 5">截取帧画</el-button>
        <el-button v-show="nowStep == 1" type="primary" @click="nowStep = 2">本地上传</el-button>
        <el-button v-show="nowStep == 1" @click="dialogVisible = false">确认</el-button>
        <el-button v-show="nowStep == 2||nowStep == 5" @click="nextStep">下一步</el-button>
        <el-button v-show="nowStep == 3||nowStep == 4||nowStep == 6" @click="nowStep = 1">完成</el-button>
        <el-button v-show="nowStep == 2||nowStep == 3||nowStep == 4||nowStep == 5||nowStep == 6" @click="dialogVisible = false">取消</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>

import _ from 'lodash'

const CANVAS_WIDTH = 400
const CANVAS_HEIGHT = 400
export default {
  data () {
    return {
      steps: [
        "",
        "海报",
        "本地上传-上传图片",
        "本地上传-设配尺寸",
        "选择尺寸",
        "截取帧画",
        "截取帧画-设配尺寸",
        "编辑",
        "海报上传"
      ],
      preventChangeTimes: 0,
      nowStep: 1,
      title: "海报",
      dialogVisible: true,
      dialogImageUrl: "",
      dialogVisible2: true,
      uploadData: {},
      fileArray: [],
      activeNames: ["1", "3"],
      sizecheckList: [],
      isMove: false,
      spanname: '',
      editorImg: '',
      br: {
        bottom: 0,
        marginLeft: 0
      },
      bl: {
        bottom: 0,
        marginLeft: 0
      },
      tl: {
        top: -4,
        marginLeft: 0
      },
      tr: {
        top: -4,
        marginLeft: 0
      },
      clip: {
        width: CANVAS_WIDTH,
        height: CANVAS_HEIGHT,
        left: 0,
        top: 0
      },
      maskL: {
        height: 0,
        top: 0,
        width: 0
      },
      maskT: {
        height: 0
      },
      maskB: {
        height: 0,
        top: 0
      },
      maskR: {
        height: 0,
        top: 0,
        left: 0
      },
      radio: 0,
      initClipData: {
        top: 0,
        left: 0,
        bottom: 0,
        right: 0,
        width: 0,
        height: 0
      }
    }
  },
  methods: {
    handleRemove (file, fileList) {
      let nodes = document.getElementsByClassName("el-upload-list__item")
      Array.from(nodes).forEach((element, index) => {
        if (index < nodes.length - 1) {
          element.setAttribute(
            "imgName",
            fileList[index].name.match(/([^;]*)\./)[1]
          )
        }
      })
    },

    change (file, fileList) {
      this.$nextTick(() => {
        if (this.preventChangeTimes !== 0) return
        let i = this.fileArray.length
        let inputDom = document.getElementsByClassName("el-upload__input")[0]
        // 允许批量上传，需要直接获取全部选中的文件
        let files = inputDom.files
        let nodes = document.getElementsByClassName("el-upload-list__item")
        Array.from(files).forEach((item, index) => {
          this.fileArray.push(inputDom.files[index])
          let imageName = inputDom.files[index].name.match(/([^;]*)\./)[1]
          let node = nodes[index + i]
          node.setAttribute("imgName", imageName)
        })
        this.preventChangeTimes = 1
      })
    },
    demo () {
      this.preventChangeTimes = 0
    },

    turnToActiveDbl (e) {
      let target = e.target.nodeName
      if (target === "IMG") {
        this.nowStep = 4
        this.$nextTick(() => {
          this.showToEditor(e)
        })
      }
    },

    turnToActive (e) {
      let target = e.target.nodeName
      console.log(target)
      if (e.target.classList.contains("img-wrap")) {
        e.target.firstElementChild.click()
      }
      if (target === "IMG") {
        let a = this.$refs["firstStep"].getElementsByClassName("active")[0]
        if (a) {
          a.classList.remove("active")
        }
        e.target.parentNode.classList.add("active")
        console.log(this.dataURLtoBlob(e.target.src))
      } else if (target === "I") {
        this.editorImg = ''
        this.nowStep = 4
      }
    },

    nextStep () {
      if (this.nowStep === 2) {
        let formData = new FormData()
        // let file = this.fileArray[0]
        let url = ""
        if (this.fileArray.length === 1) {
          url = "http://192.168.21.29:8099/poster/poster/upload"
        } else if (this.fileArray.length > 1) {
          url = "http://192.168.21.29:8099/poster/posters/upload"
        }
        this.fileArray.forEach(item => {
          formData.append("file", item)
        })
        formData.append("widthList", 400)
        formData.append("heightList", 400)
        formData.append("seriesId", 1)
        formData.append("videoNumber", 1)

        this.$http({
          url: url,
          type: 'post',
          data: formData
        })
          .then((res) => {
            let type
            if (res.data) {
              type = 'success'
            } else {
              type = 'warning'
            }
            this.$message({
              message: res.msg,
              type: type
            })
          })
      }

      this.nowStep++
    },

    // image加载进入editor里面
    showToEditor (e) {
      let _this = this
      if (e.target.classList.contains("img-wrap")) {
        e.target.firstElementChild.click()
      }
      if (e.target.nodeName === "IMG") {
        let a = this.$refs["forthStep"].getElementsByClassName("active")[0]
        if (a) {
          a.classList.remove("active")
        }
        e.target.parentNode.classList.add("active")
        let canvas1 = this.$refs.canvas1
        let ctx1 = canvas1.getContext("2d")
        let img = new Image()
        // 绘制的时候，图片的高度要读取一下
        img.onload = function () {
          ctx1.restore()
          ctx1.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
          let naturalWidth = img.naturalWidth
          let naturalHeight = img.naturalHeight
          let radio = naturalWidth / naturalHeight
          _this.radio = radio
          // console.log(radio)
          let w = CANVAS_WIDTH
          let h = CANVAS_HEIGHT
          let top = 0
          let left = 0
          // 宽 > 高
          if (radio >= 1) {
            h = CANVAS_HEIGHT / radio
            top = (CANVAS_HEIGHT - h) / 2
          // 宽 < 高
          } else {
            w = CANVAS_WIDTH * radio
            left = (CANVAS_WIDTH - w) / 2
          }

          ctx1.drawImage(img, left, top, w, h)
          _this.initClip(w, h, left, top)
        }
        img.src = e.target.src
        this.editorImg = e.target.src
      }
    },

    // 裁剪拖动相关
    onMoveStart (e) {
      this.spanname = e.target.className
      this.isMove = true
    },

    onMove: _.throttle(function (e) {
      if (!this.isMove) return
      const spanname = this.spanname

      // 调整裁剪框大小
      let moveStep = e.movementX
      let radio = this.radio
      // 真实Y移动距离
      let moveStepReaY = e.movementY
      let idata = this.initClipData
      // if (Math.abs(moveStep) > 5 || Math.abs(moveStepReaY) > 5) {
      //   moveStep = moveStep / 5
      //   moveStepReaY = moveStepReaY / 5
      // }
      let moveStepY = moveStep / radio

      if (spanname === 'photo-clip-area-bottom-right') {
        this.br.bottom -= moveStepY
        this.br.marginLeft += moveStep
        this.bl.bottom -= moveStepY
        this.tr.marginLeft += moveStep
      } else if (spanname === 'photo-clip-area-bottom-left') {
        this.bl.bottom += moveStepY
        this.bl.marginLeft += moveStep
        this.br.bottom += moveStepY
        this.tl.marginLeft += moveStep
      } else if (spanname === 'photo-clip-area-top-left') {
        this.tl.top += moveStepY
        this.tl.marginLeft += moveStep
        this.bl.marginLeft += moveStep
        this.tr.top += moveStepY
      } else if (spanname === 'photo-clip-area-top-right') {
        this.tr.top -= moveStepY
        this.tr.marginLeft += moveStep
        this.tl.top -= moveStepY
        this.br.marginLeft += moveStep
      }

      // 获取裁剪框的三个角，来计算新的高度、宽度
      let tr = this.$refs.tr
      let br = this.$refs.br
      let tl = this.$refs.tl

      // 获取新的裁剪区域的高度个宽度
      let newWeight = tr.offsetLeft - tl.offsetLeft
      let newHeight = br.offsetTop - tr.offsetTop
      // let editor = this.$refs.editor
      let newLeft = tl.offsetLeft + 4
      let newTop = tl.offsetTop + 4
      // 调整裁剪框位置
      if (spanname === 'photo-clip-area') {
        // 判断是否可以继续移动
        if ((this.maskL.width + moveStep <= idata.left && moveStep < 0) || (this.maskR.left + moveStep >= CANVAS_WIDTH - idata.right && moveStep > 0)) {
          moveStep = 0
        }

        if ((this.maskT.height + moveStepReaY <= idata.top && moveStepReaY < 0) || (this.maskB.height - moveStepReaY <= idata.top && moveStepReaY > 0)) {
          moveStepReaY = 0
        }
        newLeft += moveStep
        newTop += moveStepReaY
        // 裁剪点操作
        this.br.bottom -= moveStepReaY
        this.br.marginLeft += moveStep
        this.bl.bottom -= moveStepReaY
        this.tr.marginLeft += moveStep
        this.bl.marginLeft += moveStep
        this.tl.marginLeft += moveStep
        this.tl.top += moveStepReaY
        this.tr.top += moveStepReaY
        // 重新计算
        this.rePlaceMask(newWeight, newHeight, newLeft, newTop)
      } else {
        this.rePlaceMask(newWeight, newHeight, newLeft, newTop)
      }
      // console.log(newWeight,newHeight,newLeft,newTop)
    }, 25),

    // 重新计算mask遮罩的位置信息
    rePlaceMask (w, h, left, top) {
      this.clip.width = w
      this.clip.height = h
      this.clip.left = left
      this.clip.top = top
      // top
      this.maskT.height = top
      // left
      this.maskL.top = top
      this.maskL.height = h
      this.maskL.width = left
      // bottom
      this.maskB.top = top + h
      this.maskB.height = CANVAS_HEIGHT - this.maskB.top
      // right
      this.maskR.height = h
      this.maskR.top = top
      this.maskR.left = left + w

      // console.log(w,h,left,top)
    },

    onChangeEnd () {
      this.isMove = false
    },

    // 保存裁剪图片的坐标轴，裁剪图片
    savePhotoClip () {
      let _this = this
      let canvas1 = this.$refs.canvas1
      let ctx = canvas1.getContext("2d")
      let img = new Image()
      // 被选中的图片
      let a = this.$refs["forthStep"].getElementsByClassName("active")[0].firstElementChild
      // 绘制的时候，图片的高度要读取一下
      img.onload = function () {
        ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)

        let natureH = img.naturalHeight
        let natureW = img.naturalWidth
        // width
        let n = _this.radio > 1 ? natureW / CANVAS_WIDTH : natureW / (CANVAS_WIDTH * _this.radio)
        // heihgt
        let m = _this.radio < 1 ? natureH / CANVAS_HEIGHT : natureH / (CANVAS_HEIGHT / _this.radio)
        let sx = _this.maskT.height
        let sy = _this.maskL.width
        let sWidth = _this.clip.width
        let sHeight = _this.clip.height

        // 源图片的宽高需要对比例进行计算后取得，目标宽高就不需用了
        // console.log("裁剪的坐标信息: top: "+(sx-_this.initClipData.top) * n + ' left:' + (sy-_this.initClipData.left)*m)
        // console.log("裁剪的坐标信息: width: "+(sx-_this.initClipData.top) * n + ' left:' + (sy-_this.initClipData.left)*m)

        ctx.drawImage(img, (sy - _this.initClipData.left) * m, (sx - _this.initClipData.top) * n, sWidth * n, sHeight * m, _this.maskL.width, _this.maskT.height, _this.clip.width, _this.clip.height)

        // 设置离屏canvas:维持原图的大小
        let offscreenCanvas = document.createElement('canvas')
        let offscreenContext = offscreenCanvas.getContext('2d')

        offscreenCanvas.width = natureW
        offscreenCanvas.height = natureH

        let newImg = new Image()
        // let newWeight = _this.initClipData.width
        // let newHeight = _this.initClipData.height
        let newLeft = _this.initClipData.left
        let newTop = _this.initClipData.top
        newImg.onload = function () {
          // 这里用本身的图片的资源来裁剪，保持图片的质量
          offscreenContext.clearRect(0, 0, natureW, natureH)
          offscreenContext.drawImage(newImg, (sy - newLeft) * m, (sx - newTop) * n, sWidth * n, sHeight * m, 0, 0, natureW, natureH)
          a.onload = function () {
            // ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
            // ctx.drawImage(a, newLeft, newTop, _this.initClipData.wdidth ,_this.initClipData.height)
            // _this.rePlaceMask(newWeight,newHeight,newLeft,newTop)
            // _this.initClip()
            a.click()
          }
          a.src = offscreenCanvas.toDataURL(_this.getImageExt(0, a.src))
        }
        newImg.src = a.src
      }
      img.src = this.editorImg
    },

    // 初始化裁剪框
    initClip (width = CANVAS_WIDTH, height = CANVAS_HEIGHT, left = 0, top = 0) {
      console.log('init')
      this.br = {
        bottom: CANVAS_HEIGHT - height - top - 4,
        marginLeft: width - CANVAS_WIDTH + left - 4
      }
      this.bl = {
        bottom: CANVAS_HEIGHT - height - top - 4,
        marginLeft: left - 4
      }
      this.tl = {
        top: top - 4,
        marginLeft: left - 4
      }
      this.tr = {
        top: top - 4,
        marginLeft: width - CANVAS_WIDTH + left - 4
      }
      this.clip = {
        width: width,
        height: height,
        left: left,
        top: top
      }
      this.maskL = {
        height: top,
        top: top,
        width: left
      }
      this.maskT = {
        height: top
      }
      this.maskB = {
        height: 0,
        top: top
      }
      this.maskR = {
        height: top,
        top: top,
        left: 0
      }
      // 初始裁剪框的位置信息
      this.initClipData = {
        top: top,
        left: left,
        bottom: CANVAS_HEIGHT - height - top,
        right: CANVAS_WIDTH - left - width,
        width: width,
        height: height
      }
      console.log(JSON.stringify(this.initClipData))
      this.rePlaceMask(width, height, left, top)
    },

    // 获取图片格式
    // @e:点击事件参数  (如果在没有点击事件时候调用，传0)
    // return：toDataUrl的格式参数 (String)
    getImageExt (e, src) {
      if (e) {
        src = e.target.src
      }
      let _index = src.lastIndexOf(".")
      let ext
      console.log(_index)
      if (_index !== -1) {
        ext = src.substr(_index + 1)
      } else {
        let reg = /\/([^;]*);/
        ext = src.match(reg)[1]
      }
      if (ext === 'jpg') {
        ext = 'jpeg'
      }
      return 'image/' + ext
    },

    // base64 转 blob 格式
    dataURLtoBlob (dataURL) {
      let mine = dataURL.slice(5, dataURL.indexOf(";"))// mine格式
      let data = dataURL.split(",")[1]
      let byte = window.atob(data) // 解码
      let arraybuffers = new ArrayBuffer(byte.length)// 创建缓存数组
      let unit8 = new Uint8Array(arraybuffers)// 使用缓存数组，创建视图

      Array.from(byte).forEach((item, index) => {
        unit8[index] = byte.charCodeAt(index)
      })
      return new Blob([unit8], {type: mine})
    },

    // blob 转 base64 格式
    blobtoDataURL (blob) {
      let reader = new FileReader()
      blob.onload = function (result) {
        return result
      }
      reader.readAsDataURL(blob)
    },

    reUploadSmall () {
      this.$refs.reUploadSmallInput.click()
    }
  },
  watch: {
    nowStep (v) {
      this.title = this.steps[v]
      if (v === 2) {
        this.$nextTick(() => {
          let inputDom = document.getElementsByClassName("el-upload__input")[0]
          inputDom.click()
        })
      }
    },
    sizecheckList () {}
  },
  mounted () {
    let demo = document.getElementById("demo")
    this.$http({
      type: 'get',
      url: 'http://192.168.21.29:8099/poster/posters/page',
      data: {
        seriesId: 1,
        videoNumber: 1,
        pageNum: 1,
        pageSize: 1
      }
    })
      .then(function (res) {
        demo.src = "data:image/png;base64," + res.rows[0].base64String
      })
  }
}
</script>
<style lang="less" scoped>
.poster-content {
  height: 460px;
  overflow-y: hidden;
  .imgDiv {
    display: inline-block;
    width: 160px;
    text-align: center;
    font-size: 14px;
    color: #909399;
    .img-wrap{
      margin-right: 5px;
    }
  }
  p {
    color: #909399;
    font-size: 16px;
    margin-top: 20px;
    i {
      cursor: pointer;
    }
  }
}
.alert {
  color: red;
  font-size: 14px;
  margin-top: 20px;
  box-sizing: border-box;
  display: inline-block;
  height: 30px;
}
.choose-size {
  display: inline-block;
  height: 450px;
  font-size: 0;
  overflow: hidden;
  .left-preview {
    display: inline-block;
    width: 180px;
    background-color: #fff;
    font-size: 16px;
    height: 100%;
    .preview-content-wrap {
      display: inline-block;
      width: 100%;
      height: 434px;
      .preview-content {
        display: inline-block;
        height: 600px;
      }
    }
    .p-header {
      margin-bottom: 10px;
    }
  }
  .center-editor {
    position: relative;
    display: inline-block;
    height: 400px;
    width: 400px;
    font-size: 16px;
    vertical-align: top;
    text-align: center;
    .canvas1 {
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }
    .photo-clip-mask {
      position: absolute;
      left: 0px;
      top: 0px;
      width: 100%;
      height: 100%;
      .photo-clip-mask-left {
        position: absolute;
        left: 0px;
        right: 50%;
        top: 50%;
        bottom: 50%;
        width: auto;
        background-color: rgba(0, 0, 0, 0.5);
      }
      .photo-clip-mask-right {
        position: absolute;
        left: 50%;
        right: 0px;
        top: 50%;
        bottom: 50%;
        background-color: rgba(0, 0, 0, 0.5);
      }
      .photo-clip-mask-top {
        position: absolute;
        left: 0px;
        right: 0px;
        top: 0px;
        bottom: 50%;
        background-color: rgba(0, 0, 0, 0.5);
      }
      .photo-clip-mask-bottom {
        position: absolute;
        left: 0px;
        right: 0px;
        top: 50%;
        bottom: 0px;
        background-color: rgba(0, 0, 0, 0.5);
      }
      .photo-clip-area {
        box-sizing: border-box;
        border: 2px solid red;
        position: absolute;
        left: 50%;
        top: 50%;
        width: 260px;
        height: 260px;
        cursor: move;
        // transform: translate(-50%, -50%);
      }
      span {
        position: absolute;
        box-sizing: border-box;
        height: 8px;
        width: 8px;
        border-radius: 4px;
        border: 1px solid red;
        background-color: #fff;
      }
      .photo-clip-area-bottom-right {
        bottom: -4px;
        left: 100%;
        margin-left: -4px;
        cursor: nwse-resize;
      }
      .photo-clip-area-bottom-left {
        bottom: -4px;
        left: 0%;
        margin-left: -4px;
        cursor: nesw-resize;
      }
      .photo-clip-area-top-left {
        top: -4px;
        left: 0%;
        margin-left: -4px;
        cursor: nwse-resize;
      }
      .photo-clip-area-top-right {
        top: -4px;
        left: 100%;
        margin-left: -4px;
        cursor: nesw-resize;
      }
    }
    .blank-photo-clip-mask{
      position: absolute;
      left: 0px;
      top: 0px;
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.8);
      .contener-wrap{
        position: absolute;
        display: inline-block;
        width: 160px;
        height: 80px;
        top: 50%;
        left: 50%;
        transform: translate(-50%,-50%);
        >div{
          border:1px solid #fff;
          border-radius: 4px;
          height: 35px;
          line-height: 35px;
          margin-bottom: 10px;
          color: #fff;
          font-weight: 600px;
          font-size: 14px;
          overflow: hidden;
          &:hover{
            background-color:rgba(255, 255, 255, .3);
          }
        }
      }
    }
  }
  .center-handle{
    position: absolute;
    left: 180px;
    bottom: 0px;
    width: 400px;
    height: 60px;
    background-color: rgba(0, 0, 0, 0.8);
    // background-image: url("../../assets/bc.jpg");
    .center-handle-centent{
      display: inline-block;
      position: absolute;
      left: 50%;
      transform: translateX(-50%)
    }
  }
  .right-handle {
    display: inline-block;
    width: 180px;
    height: 100%;
    font-size: 16px;
    vertical-align: top;
    .p-header {
      margin-bottom: 15px;
    }
    .el-checkbox {
      box-sizing: border-box;
      padding-left: 10px;
      margin-left: 0px;
      width: 100%;
      margin-bottom: 10px;
    }
  }
  .p-header {
    padding-left: 20px;
  }
}
.img-wrap {
  position: relative;
  display: inline-block;
  width: 154px;
  height: 154px;
  margin: 2px;
  border: 1px dashed rgba(144,147,153,.5);
  border-radius: 6px;
  text-align: center;
  cursor: pointer;
  &.active {
    margin: 0px;
    border: 3px solid #22a2ff;
    border-radius: 6px;
  }
  img {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
    box-sizing: border-box;
    max-width: 150px;
    max-height: 150px;
  }
}
</style>
