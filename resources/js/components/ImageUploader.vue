<template>
    <div class="uploader"
        @dragenter ="OnDragEnter"
        @dragleave="OnDragLeave"
        @dragover.prevent
        @drop="OnDrop"
        :class="{ dragging: isDragging }">

        <div class="upload-control" v-show ="images.length">
            <label for="file">
                Seleccione una Imagen
            </label>
            <button @click="upload">Upload</button>
        </div>
        
        <div v-show ="!images.length">
            <i class="fa fa-cloud-upload"></i>
            <p>Arrastra tu Imagen aqui</p>
            <div>Or</div>
            <div class="file-input">
                <label for="file">Seleccione un Archivo</label>
                <input type="file" id="file" @change="onInputChange" multiple>
            </div>
        </div>
        <div class="images-preview" v-show="images.length">
            <div class="img-wrapper" v-for="(image,index) in images" :key="index">
                <img :src="image" :alt="'image Uploader $(index)'">
                <div class="details">
                    <span class="name" v-text ="files[index].name"></span>
                    <span class="size" v-text ="files[index].size"></span>

                
                
                </div>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    data: ()=>({
        isDragging : false,
        dragCount:0,
        files:[],
        images:[]


    }),
    methods: {
        OnDragEnter(e){
            e.preventDefault();
            this.dragCount ++;
            
            this.isDragging=true;
        },
        OnDragLeave(e){
            e.preventDefault();
            this.dragCount--;
           
           if(this.dragCount<=0)
            
            this.isDragging=false;
        },
        OnDrop(e){
            //console.log(e);
            e.preventDefault();
            e.stopPropagation();
            
            this.isDragging=false;
            const files =e.dataTransfer.files;
            //console.log (files);
            Array.from(files).forEach(file => this.addImage(file));

        },

        addImage(file){
            if(!file.type.match('image.*')){
                console.log('${file.name} is not an image');
                return;
            }
            this.files.push(file);

            const img=new Image(),
            reader = new FileReader();

            reader.onload=(e)=> this.images.push(e.target.result);

            reader.readAsDataURL(file);
        },

        onInputChange(e){
            const files =e.target.files;
            //console.log (files);
            Array.from(files).forEach(file => this.addImage(file));
        },
        upload(){
            const formData =new FormData();
            this.files.forEach(file=>{
                formData.append('images[]',file,file.name);
            });

            axios.post('/images-upload',formData)
            .then(response=>{
                alert("imagenes correctamente subidas");
                this.images=[];
                this.files=[];
            })

        }
    }
    
}
</script>

<style lang="scss" scoped>
.uploader{
    width: 100%;
    background: #2196f3;
    color: #fff;
    padding: 40px 15px;
    text-align: center;
    border-radius:  10px;
    border:  3px dashed #fff;
    font-size: 20px;
    position: relative; 

    &.dragging{
        background: #fff;
        color: #2196f3;
        border: 3px dashed #2196f3;

        .file-input label{
            background-color:#2196f3;
            color: #fff;

        }
    }

    i{
        font-size: 85px;
    }
    .file-input{
        width: 200px;
        margin: auto;
        height: 68px;
        position: relative;

        label,
        input{
            background: #fff;
            color:#2196f3;
            width: 100%;
            position: absolute;
            left: 0;
            top: 0;
            padding: 10px;
            border-radius:4px;
            margin-top: 7px;
            cursor: pointer;

        }
        input{
            opacity: 0;
            z-index: -2;
        }
    }

    .images-preview{
        display: flex;
        flex-wrap: wrap;
        margin-top: 20px;

        .img-wrapper{
            width:160px;
            display:flex;
            flex-direction: column;
            margin: 10px;
            height: 150px;
            justify-content: space-between;
            background: #fff;
            box-shadow: 5px 5px 20px #3e3737;

            img{
                max-height: 105px;
            }
        }
        .details{
            font-size: 12px;
            background: #fff;
            color:#000;
            display:flex;
            flex-direction: column;
            align-items: self-start;
            padding: 3px 6px;

            .name{
                overflow: hidden;
                height: 18px;
            }
        }
    }
    .upload-control{
        position: absolute;
        width: 100%;
        background: #fff;
        top:0;
        left: 0;
        border-top-left-radius: 7px;
        border-top-right-radius: 7px;
        padding:  10px;
        padding-bottom: 4px;
        text-align: right;

        button, label{
            background:  #2189f3;
            border: 2px solid #03a9f4;
            border-radius: 3px;
            color: #fff;
            font-size: 15px;
            cursor: pointer;

        }

        label {
            padding: 2px 5px;
            margin-left: 10px;
        }


    }
}





</style>

