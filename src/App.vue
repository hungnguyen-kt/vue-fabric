<template>
    <div id="app">
        <div id="container">
            <input v-if="objectSelected" class="range" type="range" v-model="filter" id="vol" name="vol" min="0" max="10" step="1" @change="handleFilter()">
            <button v-if="objectSelected" class="remove" @click="remove">Remove</button>
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
            filter: 10,
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

        handleFilter() {
            const obj = this.canvas.getActiveObject()
            obj.filters(fabric.Image.filters.Grayscale())
            obj.applyFilters(this.canvas.renderAll.bind(this.canvas));
            // if (obj.filters[5]) {
            //     obj.filters[5]['brightness'] = this.filter / 10;
            //     obj.applyFilters();
            //     this.canvas.renderAll();
            // }
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

#container .range {
    position: absolute;
    z-index: 10;
    top: 20px;
    right: 20px;
}

#container .remove {
    position: absolute;
    z-index: 10;
    top: 60px;
    right: 20px;
    display: inline-block;
    font-weight: 400;
    line-height: 1.5;
    text-align: center;
    text-decoration: none;
    vertical-align: middle;
    cursor: pointer;
    background-color: rgb(0 0 0 / 0%);
    border: 1px solid rgb(0 0 0 / 0%);
    font-size: 1rem;
    border-radius: .25rem;
    box-sizing: border-box;
    color: rgb(220 53 69);
    border-color: rgb(220 53 69);
}
</style>
