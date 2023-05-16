<template>
    <article class="w-52 text-center">
        <div
            :style="{backgroundImage : book.hasImage ? `url('${bookDetails.imageLinks.thumbnail}')` : 'none'}"
            class="relative w-full h-80 flex justify-center items-center rounded-lg bg-cover bg-center bg-gray-200 overflow-hidden group"
        >
            <Question v-if="!book.hasImage" />
            <div class="absolute w-full h-full flex justify-center items-center bg-opacity-50 bg-gray-950 hidden group-hover:flex">
                <a
                    v-if="buyButtonUrl"
                    :href="buyButtonUrl"
                    target="_blank"
                    class="mx-2 rounded-md bg-primary-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-primary-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary-500"
                >{{ buyButtonLabel }}</a>
                <button
                    v-else
                    class="cursor-not-allowed mx-2 rounded-md bg-primary-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-primary-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary-500"
                >{{ buyButtonLabel }}</button>
            </div>
        </div>
        <h1 class="text-lg font-bold mt-4 leading-6">{{ bookDetails.title }}</h1>
        <h3 class="text-sm" v-if="bookDetails.authors">{{ bookDetails.authors.join(" & ") }}</h3>
    </article>
</template>
<script setup>
    import Question from "@/assets/questionmark.vue";
</script>

<script>
export default {
    components: {
        Question
    },
    props: {
        book : {
            type : Object
        }
    },
    computed : {
        /**
         * Book object details
         */
        bookDetails() {
            return this.book.volumeInfo;
        },
        /**
         * Book object sale details
         */
        saleInfo() {
            const {saleInfo} = this.book;
            return saleInfo ?? null;
        },
        /**
         * Generate the buy button label
         */
        buyButtonLabel() {
            if( this.saleInfo ) {
                const labels = {
                    'FOR_SALE' : 'Acheter à ',
                    'FOR_SALE_AND_RENTAL' : 'Acheter / Louer à ',
                    'NOT_FOR_SALE' : 'Indisponible à la vente',
                    'FREE' : 'Gratuit'
                }
    
                var label = labels[this.saleInfo.saleability] ?? "Indisponible à la vente";
    
                if( this.saleInfo.listPrice )
                    label += this.saleInfo.listPrice.amount+" "+this.saleInfo.listPrice.currencyCode;
    
                return label
            } else
                return "Indisponible à la vente";
        },
        /**
         * Generate the buy button URL
         */
        buyButtonUrl() {
            return this.saleInfo?.buyLink ?? null;
        }
    }
}
</script>
