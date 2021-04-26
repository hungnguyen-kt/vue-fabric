<template>
    <div class="right-sidebar">
        <input id="imgUpload" type="file" accept="image/*" @change.prevent="uploadImg" />
        <hr />
        <div class="library">

        </div>
    </div>
</template>

<script>
export default {
    name: 'RightSideBarComponent',
    data() {
        return {
            images: [

            ]
        }
    },
    methods: {
        uploadImg(e) {
            this.$emit("upload-img", e)
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
    /* background-color: red; */
}
</style>
