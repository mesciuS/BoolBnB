<script>
import axios from 'axios';

export default {
    name: 'AppShow',

    data() {
        return {
            imageArray: [],
            imageArrayClean: [],
            responseMessage: '',
             house: {},
             houseId: '',
             isLoading: true,
             houseFound: false,
             imgUrl: 'http://127.0.0.1:8000/',
             form: {
                email: '',
                name: '',
                text: '',
                read: 0,
                house_id: this.$route.params.id,
             }
        }
    },

    mounted() {

          this.houseId = this.$route.params.id;
          this.getHouse();
          this.getImage();
          
    },

    computed:{
         houseImg(){

            return this.imgUrl + 'storage/' + this.house.thumbnail

        }
    },

    methods: {

        getImage() {
        axios.get("http://127.0.0.1:8000/api/image", {
            params: {
                id: this.houseId
            }
        })
        .then(res => {
            if(res.data.results.length > 0){
                this.imageArray = res.data.results
            }
            this.getMoreImage();
        });
        
},

        getMoreImage(){

            
            for (let i = 0; i < this.imageArray.length; i++) {
                const imagePath = this.imageArray[i].path;
                const imageName = 'http://127.0.0.1:8000/' + imagePath;
                
                this.imageArrayClean.push(imageName);
            }
        },
        
        submitForm(){
            console.log(this.form)
            axios.post('http://127.0.0.1:8000/api/messages', this.form).then(
                response => {
                    this.responseMessage = 'Successo';
                }
            ).catch(error => {
                this.responseMessage = 'Fallito';
            }).finally(() => {
                
            });
        },
        getHouse() {
            axios.get('http://127.0.0.1:8000/api/houses/' + this.houseId).then(res => {
                if (res.data.success) {

                    this.house = res.data.house;
                    this.houseFound = true;
                    this.isLoading = false;
                } else {
                    this.houseFound = false;
                    this.isLoading = false;
                }
            })
        }
    }
}
</script>


<template>
    <div class="my_show">
        <div v-if="isLoading" class="d-flex justify-content-center w-100">
            <div class="spinner-border my-loader" role="status">
                <span class="visually-hidden">Loading...</span>
            </div>
        </div>
        <div v-else>
            <div v-if="houseFound">

                <div class="house_container">

                    <div class="item_show">
                        <img :src="houseImg" alt="img">
    
                        <div class="my_info">
    
                            <h1 class="py-2">{{ house.title }}</h1>
                            <div class="rooms">
                                <p>{{ house.description }}</p>
                                <span>N° stanze: {{ house.rooms}}</span><br>
                                <span>N° posti letto: {{ house.beds}}</span><br>
                                <span>N° bagni: {{ house.bathrooms }}</span><br>
                                <span>Indirizzo:  {{ house.street}} , {{house.house_number}} - {{house.city}}</span>
    
                                <div>Servizi: </div>
                                <div v-for="service in house.services">
                                    <i :class="service.icon" class="py-2"></i> - {{ service.name }}
                                </div>
    
                            </div>
                            
                            <div>
                                <!-- Button trigger modal -->
                                <div class="text-center">
                                    <button type="button" class="btn" data-bs-toggle="modal" data-bs-target="#messageModal">
                                        Invia un messaggio
                                    </button>

                                </div>
                                
                                <!-- Modal -->
                                <div class="my_modal modal fade" id="messageModal" tabindex="-1" aria-labelledby="messageLabel" aria-hidden="true">
                                    <div class="modal-dialog modal-dialog-centered">
                                        <div class="modal-content">
                                            <div class="modal-header">
                                                <h1 class="modal-title fs-5" id="messageLabel">Invia un messaggio</h1>
                                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                                            </div>
                                            <div class="modal-body">
                                                <div class="message mx-1">
                                                    <form class="d-flex flex-column gap-2" v-on:submit.prevent="submitForm" v-if="this.responseMessage == ''|| this.responseMessage == 'Fallito'">
                                                        <div class="form-group">
                                                            <label for="exampleFormControlInput1">
                                                                Email:
                                                            </label>
                                                            <input type="email" class="form-control" id="exampleFormControlInput1" placeholder="nome@mail.com" v-model="form.email">
                                                        </div>
                                                        <div class="form-group">
                                                            <label for="exampleFormControlInput1">
                                                                Nome:
                                                            </label>
                                                            <input type="text" class="form-control" id="exampleFormControlInput1" placeholder="Aggiungi un nome" v-model="form.name">
                                                        </div>
                                                        <div class="form-group">
                                                            <label for="exampleFormControlTextarea1">
                                                                Messaggio:
                                                            </label>
                                                            <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="form.text"></textarea>
                                                        </div>
                                                        <button type="submit" class="btn btn-secondary d-flex align-self-center">Invia</button>
                                                    </form>
                                                    <h2 v-else class="text-center alert alert-success">Messaggio inviato con successo!</h2>
                                                    
                                                    <h2 v-show="this.responseMessage == 'Fallito'" class="text-center alert alert-danger mt-3">Invio fallito!</h2>
                                                </div>
                                            </div>
                                            <div class="modal-footer">
                                                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Chiudi</button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                
                            </div>
                        
                        
                        </div>
    
                    </div>
                    <div class="other_images">
                        <div v-for="image in imageArrayClean" class="single_image">
                            <img :src="image" alt="image">
                        </div>
                    </div>
                    <div class="mobile-info">
                        <h1 class="py-2">{{ house.title }}</h1>
                            <div class="rooms">
                                <p>{{ house.description }}</p>
                                <span>N° stanze: {{ house.rooms}}</span><br>
                                <span>N° posti letto: {{ house.beds}}</span><br>
                                <span>N° bagni: {{ house.bathrooms }}</span><br>
                                <span>Indirizzo:  {{ house.street}} , {{house.house_number}} - {{house.city}}</span>
    
                                <div>Servizi: </div>
                                <div v-for="service in house.services">
                                    <i :class="service.icon" class="py-2"></i> - {{ service.name }}
                                </div>
    
                            </div>
                            
                            <div>
                                <!-- Button trigger modal -->
                                <div class="text-center">
                                    <button type="button" class="btn" data-bs-toggle="modal" data-bs-target="#messageModal">
                                        Invia un messaggio
                                    </button>

                                </div>
                                
                                <!-- Modal -->
                                <div class="my_modal modal fade" id="messageModal" tabindex="-1" aria-labelledby="messageLabel" aria-hidden="true">
                                    <div class="modal-dialog modal-dialog-centered">
                                        <div class="modal-content">
                                            <div class="modal-header">
                                                <h1 class="modal-title fs-5" id="messageLabel">Invia un messaggio</h1>
                                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                                            </div>
                                            <div class="modal-body">
                                                <div class="message mx-1">
                                                    <form class="d-flex flex-column gap-2" v-on:submit.prevent="submitForm" v-if="this.responseMessage == ''|| this.responseMessage == 'Fallito'">
                                                        <div class="form-group">
                                                            <label for="exampleFormControlInput1">
                                                                Email:
                                                            </label>
                                                            <input type="email" class="form-control" id="exampleFormControlInput1" placeholder="nome@mail.com" v-model="form.email">
                                                        </div>
                                                        <div class="form-group">
                                                            <label for="exampleFormControlInput1">
                                                                Nome:
                                                            </label>
                                                            <input type="text" class="form-control" id="exampleFormControlInput1" placeholder="Aggiungi un nome" v-model="form.name">
                                                        </div>
                                                        <div class="form-group">
                                                            <label for="exampleFormControlTextarea1">
                                                                Messaggio:
                                                            </label>
                                                            <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="form.text"></textarea>
                                                        </div>
                                                        <button type="submit" class="btn btn-secondary d-flex align-self-center">Invia</button>
                                                    </form>
                                                    <h2 v-else class="text-center alert alert-success">Messaggio inviato con successo!</h2>
                                                    
                                                    <h2 v-show="this.responseMessage == 'Fallito'" class="text-center alert alert-danger mt-3">Invio fallito!</h2>
                                                </div>
                                            </div>
                                            <div class="modal-footer">
                                                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Chiudi</button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                
                            </div>
                    </div>
                </div>

                
            </div>
            <div v-else>
                <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
                    <symbol id="exclamation-triangle-fill" viewBox="0 0 16 16">
                        <path
                            d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767L8.982 1.566zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5zm.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2z" />
                    </symbol>
                </svg>
                <div class="alert alert-warning d-flex align-items-center" role="alert">
                    <svg class="bi flex-shrink-0 me-2" role="img" aria-label="Warning:">
                        <use xlink:href="#exclamation-triangle-fill" />
                    </svg>
                    <div>
                        File not found !!!
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>


<style lang="scss" scoped>

@import "../scss/variables";
@import "../scss/mixins";

.my-loader{
    color: $accent;
    margin: 5rem 0;
}

.house_container{
    max-width: 1200px;
    margin: 2rem auto;

    display: flex;
    flex-direction: column;
    .item_show{
        @include border();
        
        position: relative;
    
        .my_info{
            background-color: $transparent;
            padding: 20px;
            @include border();
            max-height: 600px;
    
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            position: absolute;
            top: 0px;
            right: 0px;
    
            height: 700px;
            max-width: 400px;
    
            margin: 40px;
            font-weight: bold;

        }
    
        img{
            aspect-ratio: 4/3;
            object-fit: cover;
            width: 100%;
            border-radius: 30px;
        }
    
        .btn{
            @include border();
    
            background-color: $accent;
            color: white;
            padding: 10px 15px;

            &:hover{
                background-color: darkolivegreen
            }
        }
    
        .my_modal{
            @include border();
        }
    }

    .other_images{
        width: 100%;
        display: flex;
        gap: 20px;

       .single_image{
            margin: 1rem 0;
            width: calc(100% / 3 - 20px / 3 * 2);

            img{
                aspect-ratio: 4/3;
                width: 100%;
                object-fit: cover;
                @include border();
            }
        }
    }

    .mobile-info {
        display: none;
    }

    @media screen and (max-width: 992px) {
        .item_show {
            max-width: 800px;
            margin: 0 auto;
            .my_info {
                padding: 20px;
                margin: 0;
                font-weight: bold;
            }
        }
        .other_images {
            max-width: 800px;
            margin: 0 auto;

        }
    }
    @media screen and (max-width: 576px) {
        .item_show {
            max-width: 500px;
            margin: 0 auto;
            .my_info {
                padding: 20px;
                margin: 0;
                font-weight: bold;
                display: none;
            }
        }
        .other_images {
            max-width: 500px;
            margin: 0 auto;

        }
        .mobile-info {
            background-color: $transparent;
            padding: 20px;
            @include border();
            max-width: 500px;
            margin: 0 auto;
            display: block;
            .btn{
            @include border();
            margin-top: 10px;
            background-color: $accent;
            color: white;
            padding: 10px 15px;

            &:hover{
                background-color: darkolivegreen
            }
        }
    
        .my_modal{
            @include border();
        }
        }
    }

}    



</style>