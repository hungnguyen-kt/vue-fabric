<template>
    <div id="app">
        <div id="container">
            <canvas ref="c" id="canvas" width="800" height="800"></canvas>
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

export default {
    name: 'App',
    components: { RightSideBarComponent },
    data() {
        return {
            canvas: null,
            config: {
                borderColor: '#000',
                cornerColor: '#000',
                cornerStyle: 'circle'
            },
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
</style>
