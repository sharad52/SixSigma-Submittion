<template>
    <div class="container mx-auto bg-white">
        <div class="flex flex-col justify-center bg-white w-4/5 m-auto">
            <h2>multiple Images Upload and rendering </h2>
        
            <div>
                <label>
                    <span>Select a File</span>
                    <input class="hidden" @change="imageChange" type="file" name="image" ref="files" multiple/>
                </label>
            </div>
            <div class="m-auto">
                <p v-for="(image,index) in images" :key="index">{{image.name}}</p>
            </div>

            <div class="flex items-center justify-between m-auto">
                <button @click="uploadImages" class="btn btn-info" >
                    Upload
                </button>
            </div>
            <table class="table" v-if="pictures.length>=1">
                <thead>
                    <tr>
                        <th>Id</th>
                        <th>Images</th>
                        <th>Actions</th>
                    </tr>
                    
                </thead>
                <tbody>
                    <tr v-for="(picture,index) in pictures" :key="index">
                        <td> {{picture.id}}</td>
                        <td><img v-for="(img,index) in picture.images" :key="index" :src="img"></td>
                        <td><i @click="deleteImage(picture.id)" class="fa fa-times"></i></td>
                    </tr>
                </tbody>
            </table>
        </div>
        
    </div>
</template>
<script>
    export default {
        data() {
            return {

                images:[],
                pictures:''
             }
        },
        methods:{

            imageChange(){
                for (let i = 0; i < this.$refs.files.files.length; i++) {
                    this.images.push(this.$refs.files.files[i]);

                    console.log(this.images);
                    
                }
            },
            uploadImages(){

                var self = this;

                let formData = new FormData();
                for (let i = 0; i < this.images.length; i++) {
                    
                    let file = self.images[i];
                    formData.append('files['+ i +']',file); 
        
                }

                const config = {
                    headers:{"content-type":"multipart/form-data"}
                }
                axios
                .post('/api/images',formData,config)
                .then(response => {

                    self.$refs.files.value ='';
                    self.images = [];
                    self.getImages();
                })
                .catch(error => {
                    console.log(error);
                })
            },

            getImages(){

                axios
                .get('/api/images')
                .then(response => {

                    this.pictures = response.data.images;

                })
                .catch(error => {

                })
            },
            deleteImage(id){

                axios
                .delete('/api/images/${id}')
                .then(response=> {
                    this.getImages();
                })
                .catch(error => {
                    console.log(error);
                })
            }
        },
        created(){

            this.getImages();
        }
        
    }
</script>