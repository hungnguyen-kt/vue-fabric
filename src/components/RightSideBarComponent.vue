<template>
    <div class="right-sidebar">
        <input id="imgUpload" type="file" accept="image/*" @change.prevent="uploadImg" />
        <hr />
        <div class="library">
            <h3>Select Images from Library</h3>
            <div class="imgs">
                <div class="img" v-for="img in images" :key="img">
                    <img :src="require(`@/assets/${img}`)" alt="" @click="$emit('selectLibrary', require(`@/assets/${img}`))">
                </div>
            </div>
        </div>
        <hr />
        <div class="text-box">
            <form @submit.prevent="addText()">
                <textarea v-model.trim="text" placeholder="Input text here"></textarea>
                <button type="button" @click="addText()">Add Text</button>
            </form>
        </div>
    </div>
</template>

<script>
export default {
    name: 'RightSideBarComponent',
    data() {
        return {
            text: '',
            images: [
                'images/TopHat2.svg',
                'images/Girl4.svg',
                'images/ComicCharacter411.svg',
                'images/Church12.svg',
                'images/JPMorgan.svg',
                'images/Stationmaster.svg'
            ]
        }
    },

    methods: {
        uploadImg(e) {
            this.$emit("upload-img", e)
        },

        addText() {
            if (!this.text.trim()) return
            this.$emit('addText', this.text)
        }
    }
}
</script>

<style>
.right-sidebar {
    width: 300px;
    height: 100vh;
    border-left: 1px solid #000;
    padding: 8px;
    box-sizing: border-box;
}

.library,
.imgs {
    width: 100%;
}

.imgs {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-content: baseline;
    justify-content: space-between;
}

.imgs .img {
    max-width: calc(100% / 2 - 4px);
    max-height: 100px;
    overflow: hidden;
    padding: 4px;
    box-sizing: border-box;
    border: 1px solid #000;
    margin-bottom: 8px;
    border-radius: 4px;
    cursor: pointer;
}

.imgs .img img {
    width: 100%;
    height: 100%;
}

form textarea {
    resize: vertical;
    display: block;
    width: 100%;
    max-width: inherit;
    padding: .375rem .75rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: rgb(33 37 41);
    background-color: rgb(255 255 255);
    border: 1px solid rgb(206 212 218);
    border-radius: .25rem;
    margin-bottom: 16px;
    box-sizing: border-box;
}

form button {
    display: inline-block;
    font-weight: 400;
    line-height: 1.5;
    text-align: center;
    text-decoration: none;
    vertical-align: middle;
    cursor: pointer;
    background-color: rgb(0 0 0 / 0%);
    border: 1px solid rgb(0 0 0 / 0%);
    padding: .375rem .75rem;
    font-size: 1rem;
    border-radius: .25rem;
    box-sizing: border-box;
    color: rgb(25 135 84);
    border-color: rgb(25 135 84);
}
</style>
