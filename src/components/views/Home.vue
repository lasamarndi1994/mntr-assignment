<template>
    <div class="container">
        <div class="row col-md-8">
            <div class="mt-2 d-flex justify-content-center">
                <div class="col  col-md-4" v-for="(book,index) in books" :key="index">
                    <button type="button" @click="showBookChapter(index)" class="btn btn-secondary  btn-sm"
                        :class="{ active: active_el == index}">
                        {{ book.title }}
                    </button>
                </div>
            </div>

            <div class="mt-3 d-flex justify-content-center">
                <div class="" v-for="(chapter_id,index) in chapter_ids" :key="index">
                    <button type="button" @click="showChapter(chapter_id,index)"
                        :class="{ active: active_el_chapter == index}" class="mr-2">{{ chapter_id }}</button>
                </div>
            </div>
            <div class="mt-3 d-flex justify-content-center">
                <div class="image-block" v-if="pages.length > 0 ">
                    <a href="#" type="button" class="prev-click" @click="prevPage()"></a>
                    <img :src="current_page.image.file" width="650" height="450">
                    <a href="#" class="next-click" @click="nextPage()"></a>
                </div>
            </div>
        </div>
    </div>

</template>

<script>

import axios from "axios";
const base_url= 'http://18.177.140.79:8080';
export default {

    data(){
        return {
            books:[],
            chapter_ids:[],
            chapters:[],
            pages:[],
            active_el:0,
            current_page : {},
            next_page :0,
            active_el_chapter:0,
        }
    },
    created() {
        this.getAllBooks();
        this.showChapter(1);
    },
    methods:{
        nextPage(){
            if (this.next_page == 0 || this.next_page >0) {
                this.next_page  ++;
                this.current_page = this.pages[this.next_page];
            }
        
        },
        prevPage() {
            if(this.next_page > 0){
                this.next_page--;
                this.current_page  = this.pages[this.next_page];
            }
        },
        showBookChapter(index){
            this.active_el = index;
            this.active_el_chapter = 0;
            this.chapter_ids = this.books[index].chapter_ids;
            this.showChapter(this.chapter_ids[0]);

        },
        showChapter(chapter_id,index=0){ 
            this.active_el_chapter = index;
            axios.get(`${base_url}/chapters/${chapter_id}/`).then((response) => {   
                this.current_page = response.data.pages[0];
                this.pages = response.data.pages;
            })
        },

        getAllBooks(){
            axios.get(`${base_url}/books/`).then((response) =>{
                this.books = response.data;
                this.chapter_ids = response.data[0].chapter_ids;
                this.active_el_chapter=0;
            })
        }
    }

}
</script>
<style scoped>




.active {
    background-color: green;
}
div {
    display: inline-block;
    position: relative;
}



.next-click {
    position: absolute;
    top: 15%;
    right: 0;
    left: 78%;
    width: 5em;
    height: 25em;
}
.prev-click {
    position: absolute;
    top: 15%;
    left: 0%;
    right: 75%;
    width: 10em;
    height: 25em;
}

</style>
