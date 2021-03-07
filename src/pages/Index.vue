<template>

  <div class="fit row wrap justify-center items-start content-start" >
      <q-drawer
        v-model="isOpen"
        show-if-above
        bordered
        content-class="bg-white text-black"
      >
      <div class="row wrap justify-center items-start content-start q-gutter-x-md q-gutter-y-md q-pa-xl">
        <q-btn icon="add" @click="cropper.zoom(0.1)" class="bg-primary text-white " />
        <q-btn icon="remove" @click="cropper.zoom(-0.1)"  class="bg-primary text-white" />
        <div class="full-width"></div>
        <q-btn round  icon="undo" @click="cropper.rotate(-6)" class="bg-primary text-white" />
        <q-btn round  icon="redo" @click="cropper.rotate(6)"  class="bg-primary text-white" />
        <q-input v-model="filename" label="Filename"  class="full-width"/>
        <q-input v-model="title" label="Title"  class="full-width"/>
        <q-input v-model="floors" label="Floors" class="full-width" />
          <q-slider
          v-model="height_bottom"
          :min="0.6"
          :max="0.9"
          :step="0.01"
          label
          :label-value="Math.round((1-height_bottom)*100) + '%'"
          label-always
          class="q-mt-xl"
        />
        <q-slider
          v-model="height_number"
          :min="0.33"
          :max="3.0"
          :step="0.1"
          label
          :label-value="Math.round((height_number)*100) + '%'"
          label-always
          class="q-mt-xl"
        />

        <q-btn class="bg-primary text-white full-width" @click="index=0" label="Download"><a v-if="destination" download="FILENAME.png" :href="destination"></a></q-btn>

      </div>

      </q-drawer>
    <q-card class="q-mt-xl" >
      <q-card-section>
        <img ref="img" :src="src" alt="" />
      </q-card-section>
    </q-card>
    <div class="col-12"/>
    <q-card class="q-mt-xl q-pa-none" >
      <q-card-section>
              <!-- <span class="full-width full-height absolute" style="height:100%;w" ><svg viewBox="0 0 16 16" width="16" height="16" class="icon-inline icon-inline--on-left "><path d="M14 8h1v7H1V1h7v1H2v12h12zm-4-7v1h3.29L7.62 7.67l.71.71L14 2.71V6h1V1z"></path></svg>
      Open in Map Viewer</span> -->
        <canvas :src="destination"  id="viewport" ref="canvas" height="400" width="600" style="max-width:200px"/>
      </q-card-section>

    </q-card>

  </div>
</template>

<script>
import Cropper from 'cropperjs'

export default {
  name: 'MainPage',
  props: ['drawer'],
  data () {
    return {
      isOpen: true,
      src: 'https://maps.schiphol.nl/portal/portalimages/desktopapp.png',
      cropper: {},
      destination: {},
      image: {},
      ctx: null,
      title: 'Indoor Public',
      serie: null,
      floors: ['-10', '0', '10'],
      w: 600,
      h: 400,
      height_bottom: (1 - 0.23),
      height_number: 1.5,
      index: null,
      filename: 'Indoor_Public_Basemap_{floor}'
    }
  },

  mounted () {
    // this.isOpen = this.props.drawer
    this.ctx = this.$refs.canvas.getContext('2d')
    this.$refs.canvas.height = this.h
    this.$refs.canvas.width = this.w
    this.image = this.$refs.img
    this.serie = this.floors[0]
    this.cropper = new Cropper(this.image, {
      zoomOnWheel: false,
      aspectRatio: 6 / 4,
      guides: false,
      // responsive: true,
      background: false,
      crop: () => {
        this.render()
      }
    })
  },
  created () {
    window.addEventListener('paste', (e) => { this.paste_clipboard(e) }, false)
  },
  methods: {
    changeValue: function () {
      this.isOpen = !this.isOpen
      this.$emit('drawer', this.isOpen)
    },
    render: function (download) {
      this.drawImage(this.cropper.getCroppedCanvas({ width: this.w, height: this.h }).toDataURL('image/png'), download)
    },
    drawImage: function (base64, download) {
      var image = new Image()
      image.onload = () => {
        this.ctx.drawImage(image, 0, 0, 600, 400)
        this.drawBottom(download)
      }
      image.src = base64
    },
    drawBottom: function (download) {
      this.ctx.fillStyle = '#ddd'
      this.ctx.fillRect(0, this.h * this.height_bottom, this.w, this.h * (1 - this.height_bottom))

      var border = 1

      var grd = this.ctx.createLinearGradient(0, 0, this.w, 0)
      grd.addColorStop(0, 'rgb(0 97 155)')
      grd.addColorStop(1, 'rgb(0 122 194)')
      this.ctx.fillStyle = grd
      this.ctx.fillRect(0 + border, this.h * this.height_bottom + border, this.w - border * 2, this.h * (1 - this.height_bottom) - border * 2)

      this.ctx.fillStyle = 'rgba(255, 255, 255, 1)'
      var fontSize = this.h * (1 - this.height_bottom) * 0.5
      this.ctx.font = fontSize + 'px Roboto'
      this.ctx.fillText(this.title, fontSize, this.h - fontSize * 0.7)
      this.drawSerie()
      this.getIcon('maps16', download)
    },
    drawSerie: function (params) {
      if (this.floors.length === 0 || this.floors[0] === '') this.serie = ''
      var fontSize = this.h * (1 - this.height_bottom) * 0.7 * this.height_number
      this.ctx.lineWidth = 8
      this.ctx.fillStyle = 'rgba(255, 255, 255, 1)'
      this.ctx.font = fontSize + 'px Roboto'
      this.ctx.strokeStyle = 'rgba(0, 0, 0, 1)'
      this.ctx.strokeText(this.serie, fontSize, this.h * this.height_bottom - fontSize * 0.7)
      this.ctx.fillText(this.serie, fontSize, this.h * this.height_bottom - fontSize * 0.7)
    },
    getIcon: function (type, download) {
      var img = new Image()
      var ratio = 0.9
      var topBottom = this.h * (this.height_bottom)
      var heightBottom = this.h * (1 - this.height_bottom)
      var padding = heightBottom * (1 - ratio) / 2
      img.onload = () => {
        this.ctx.drawImage(img, this.w - (heightBottom * ratio) - padding, topBottom + padding, heightBottom * ratio, heightBottom * ratio)
        if (download) this.download()
      }
      img.src = '/symbols/maps16.svg'
    },
    // getSize: function () {
    //   this.w = 600 // this.$refs.canvas.getBoundingClientRect().width
    //   this.h = 400 // this.$refs.canvas.getBoundingClientRect().height
    // },
    downloadAll: function () {
      console.log(this.index, this.floors[this.index])
      if (typeof this.floors[this.index] === 'undefined') return
      this.serie = this.floors[this.index]
      this.render(true)
    },
    download: function () {
      var link = document.createElement('a')
      link.download = (this.serie.length > 0) ? this.filename.replace('{floor}', this.serie) + '.png' : this.filename.replace('{floor}', '') + '.png'
      link.href = this.$refs.canvas.toDataURL('image/png').replace('image/png', 'image/octet-stream')
      console.log(link.download, this.serie)
      this.index += 1
      link.click()
      // this.downloadAll()
    },
    paste_clipboard: function (e) {
      if (e.clipboardData) {
        var items = e.clipboardData.items
        if (!items) return

        // access data directly
        var isImage = false
        for (var i = 0; i < items.length; i++) {
          if (items[i].type.indexOf('image') !== -1) {
            // image
            var blob = items[i].getAsFile()
            var URLObj = window.URL || window.webkitURL
            var source = URLObj.createObjectURL(blob)
            console.log(source)
            // this.paste_createImage(source)
            isImage = true
          }
        }
        if (isImage === true) {
          e.preventDefault()
        }
      }
    }
    // clipboard_to_image: function (source) {
    //   var pastedImage = new Image()
    //   pastedImage.onload = function () {
    //     if (autoresize == true) {
    //       // resize
    //       canvas.width = pastedImage.width
    //       canvas.height = pastedImage.height
    //     } else {
    //       // clear canvas
    //       ctx.clearRect(0, 0, canvas.width, canvas.height)
    //     }
    //     ctx.drawImage(pastedImage, 0, 0)
    //   }
    //   pastedImage.src = source
    // }

  },
  watch: {
    height_bottom: function () { this.render() },
    height_number: function () { this.render() },
    title: function () { this.render() },
    floors: function (v) { this.floors = v.replace(' ', '').split(','); this.serie = this.floors[0]; this.render() },
    index: function () { this.downloadAll() }
  }
}
</script>
<style lang="sass" scoped>
a
  text-decoration: none
  color: white
.input
  // max-width: 200px !important
  max-height: 200px !important
.result
  canvas
    max-width: 200px
    width: 100vw

</style>
