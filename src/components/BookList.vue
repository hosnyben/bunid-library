<template>
    <section class="w-full max-w-6xl mt-10">
        <div class="flex flex-wrap gap-7 justify-center">
            <BookItem v-for="(book,key) in simpleBooks" :key="key" :book="book" />
        </div>
        <Pagination
            v-if="simpleBooks.length"
            :page="currentPage"
            :count="totalPages"
            @change="changePage"
        />
    </section>
</template>
<script setup>
    import BookItem from "./BookItem.vue";
    import Pagination from "./Pagination.vue";
</script>

<script>
export default {
    components: {
        BookItem,
        Pagination
    },
    data() {
    },
    props: {
        books : {
            type : Array
        },
        currentPage : {
            type : Number
        },
        totalPages : {
            type: Number
        }
    },
    computed: {
        simpleBooks() {
            return this.books.map((book) => {
                return {
                    ...book,
                    ...{hasImage : (book.volumeInfo.imageLinks) ? true : false}
                }
            })
        }
    },
    methods: {
        changePage(val) {
            this.$emit('changePage',val);
        }
    }
}
</script>
