<template>
    <div class="flex flex-col items-center justify-center px-6 py-8 mx-auto md:min-h-screen">
        <SearchBar
            v-model="searchTarget"
            @search="searchIt"
        />
        <Loader v-if="loading"/>
        <BookList
            v-else
            :books="books"
            :currentPage="currentPage"
            :totalPages="totalPages"
            @changePage="changePage"
        />
        
    </div>
</template>
<script>
import SearchBar from "@/components/SearchBar.vue";
import BookList from "@/components/BookList.vue";
import Loader from "@/components/Loader.vue";
import { debounce } from "lodash";
import axios from 'axios';

export default {
    components: {
        SearchBar,
        BookList,
        Loader
    },
    data() {
        return {
            loading: false, // Bool - When fetch query is performing it turns to true
            searchTarget : '', // String
            books : [],

            currentPage : 1,
            oldCurrentPage : 1,

            totalPages : 0,
            booksPerPage : 10
        }
    },
    /**
     * Track changes of the search query or the current page to run the query over Google Api
     */
    watch : {
        searchTarget: {
            async handler (){
                this.currentPage = 1;
                this.oldCurrentPage = 1;
                await this.write();
            }
        },
        currentPage: {
            async handler (val) {
                if( val !== this.oldCurrentPage ){
                    await this.write(false);
                }
            }
        }
    },
    methods: {
        async searchIt() {
            this.currentPage = 1;
            this.oldCurrentPage = 1;
            await this.search();
        },
        /**
         * Debounce the method to optimise amount of queries.
         */
        write: debounce(async function (generatePagination = true) {
            await this.search(generatePagination);
        }, 500),
        async search(generatePagination) {
            this.loading = true;
            const apiKey = 'AIzaSyAfcxBu6TSgFOhB3sRvXzRIJCnKuPbGmWY';

            await axios.get(`https://www.googleapis.com/books/v1/volumes`, {
                params : {
                    key : apiKey,
                    q : encodeURIComponent(this.searchTarget),
                    startIndex : (this.currentPage - 1)*this.booksPerPage,
                    maxResults : this.booksPerPage,
                    printType: 'books'
                }
            }).then(({data}) => {
                this.loading = false;

                /**
                 * Due to consistence issue of Google Book Api pagination, the system can return a good amount of pages
                 * which is not the case when you try to access to that page. So to fix this issue we had to check if the query returns books or not.
                 * If not, we go back to previous page.
                 */
                
                if( data.items ) {
                    this.books = data.items;
                    this.oldCurrentPage = this.currentPage;
                } else {
                    this.currentPage = this.oldCurrentPage;
                }

                /**
                 * The number of pages changes after each query, that's why we update the page count on first query.
                 * We need to make sure to keep the initial page count even on page change.
                 */

                if( generatePagination )
                    this.totalPages = Math.ceil(data.totalItems / this.booksPerPage);
            }).catch(() => {
                this.loading = false;
            })
        },
        changePage(val) {
            this.currentPage = val;
        }
    }
}
</script>
