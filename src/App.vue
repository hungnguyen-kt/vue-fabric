<template>
    <div id="app">
        <div id="container">
            <canvas ref="c" id="canvas" width="800" height="800"></canvas>
            <bottom-bar-component v-if="objectSelected" @filters="handleFilter" @remove="remove" />
        </div>
        <right-side-bar-component
            @upload-img="uploadImg"
            @selectLibrary="uploadImgWithSVG"
            @addText="addText"
        />
    </div>
</template>

<script>
import {fabric} from 'fabric'
import RightSideBarComponent from '@/components/RightSideBarComponent'
import BottomBarComponent from './components/BottomBarComponent'

export default {
    name: 'App',
    components: { RightSideBarComponent, BottomBarComponent },
    data() {
        return {
            canvas: null,
            filter: 0.1,
            config: {
                borderColor: '#000',
                cornerColor: '#000',
                cornerStyle: 'circle'
            },
            objectSelected: null,
            angle: 0
        }
    },

    mounted() {
        window.addEventListener('resize', this.initCanvas)

        this.$nextTick(() => {
            const ref = this.$refs.c
            this.canvas = new fabric.Canvas(ref, { backgroundColor: '#cdcdcd' })
            this.initCanvas()

            this.canvas.on({
                'object:moving': (e) => {
                    e.target.opacity = 0.5
                },
                'object:modified': (e) => {
                    e.target.opacity = 1
                },
                'selection:created': (e) => {
                    this.objectSelected = e
                },
                'selection:cleared': () => {
                    this.objectSelected = null
                },
                'mouse:wheel': (opt) => {
                    const delta = opt.e.deltaY;
                    let zoom = this.canvas.getZoom();
                    zoom *= 0.999 ** delta;
                    if (zoom > 20) zoom = 20;
                    if (zoom < 0.01) zoom = 0.01;
                    this.canvas.setZoom(zoom);
                    opt.e.preventDefault();
                    opt.e.stopPropagation();
                }
            })
        })
    },

    methods: {
        initCanvas() {
            if (!this.canvas) return

            this.canvas.setDimensions({
                width: window.innerWidth - 301,
                height: window.innerHeight
            })
        },

        uploadImg(e) {
            const fileType = e.target.files[0].type
            const listFileTypes = ['image/jpeg', 'image/png', 'image/svg+xml']
            if (!listFileTypes.includes(fileType)) return
            const url = URL.createObjectURL(e.target.files[0])

            if (fileType === 'image/svg+xml') {
                this.uploadImgWithSVG(url)
            } else {
                this.uploadImgNormal(url)
            }
        },

        uploadImgWithSVG(url) {
            fabric.loadSVGFromURL(url, (objects, options) => {
                let svg = fabric.util.groupSVGElements(objects, options)
                svg.set({ ...this.config })
                this.canvas.add(svg)
            })
        },

        uploadImgNormal(url) {
            fabric.Image.fromURL(url, (myImg) => {
                myImg.set({ ...this.config })
                this.canvas.add(myImg)
            })
        },

        addText(text) {
            const textbox = new fabric.Textbox(text, {
                left: 50,
                top: 50,
                ...this.config
            });

            this.canvas.add(textbox);
        },

        handleFilter(brightness) {
            const obj = this.canvas.getActiveObject()
            if (!obj.filters) return
            obj.filters[0] = new fabric.Image.filters.Brightness({ brightness: brightness })
            obj.applyFilters()
            this.canvas.requestRenderAll()
        },

        remove() {
            this.canvas.remove(this.canvas.getActiveObject())
        }
    }
}
</script>

<style>
body {
    margin: 0;
    padding: 0%;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    display: flex;
    flex-direction: row;
}

#container {
    position: relative;
    top: 0;
    left: 0;
}

#container canvas {
    z-index: 0;
}

.btn {
    display: inline-block;
    font-weight: 400;
    line-height: 1.5;
    text-align: center;
    text-decoration: none;
    vertical-align: middle;
    background-color: rgb(0 0 0 / 0%);
    border-width: 1px;
    border-style: solid;
    padding: .375rem .75rem;
    font-size: 1rem;
    border-radius: .25rem;
}
</style>
