<template>
    <section>
        <div class="p-4 mb-4 text-yellow-800 border border-yellow-300 rounded-lg bg-yellow-50 dark:bg-gray-800 dark:text-yellow-300 dark:border-yellow-800 mt-10" role="alert">
            <div class="flex items-center">
                <svg aria-hidden="true" class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"></path></svg>
                <span class="sr-only">Important !</span>
                <h3 class="text-lg font-medium">Google Book API Pagination</h3>
            </div>
            <div class="mt-2 mb-4 text-sm">Il y a une incohérence dans le système de pagination de l'API Google Book. Si vous remarquez que le système vous ramène à la page précédemment sélectionnée, c'est normal. C'est une solution temporaire pour contourner ce problème.</div>
            <div class="flex">
                <a 
                    target="_blank"
                    href="https://www.loom.com/share/9fba647e3e97457b88595681d22f5fdd"
                    class="text-white bg-yellow-800 hover:bg-yellow-900 focus:ring-4 focus:outline-none focus:ring-yellow-300 font-medium rounded-lg text-xs px-3 py-1.5 mr-2 text-center inline-flex items-center dark:bg-yellow-300 dark:text-gray-800 dark:hover:bg-yellow-400 dark:focus:ring-yellow-800"
                >
                    <svg aria-hidden="true" class="-ml-0.5 mr-2 h-4 w-4" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path d="M10 12a2 2 0 100-4 2 2 0 000 4z"></path><path fill-rule="evenodd" d="M.458 10C1.732 5.943 5.522 3 10 3s8.268 2.943 9.542 7c-1.274 4.057-5.064 7-9.542 7S1.732 14.057.458 10zM14 10a4 4 0 11-8 0 4 4 0 018 0z" clip-rule="evenodd"></path></svg>
                    Voir plus
                </a>
            </div>
        </div>
        <nav class="flex items-center justify-between border-t border-gray-200 px-4 sm:px-0 mt-10">
            <div class="-mt-px flex w-0 flex-1">
                <span
                    v-if="page > 1"
                    @click="changePage(page-1)"
                    class="cursor-pointer inline-flex items-center border-t-2 border-transparent pr-1 pt-4 text-sm font-medium text-gray-500 hover:border-gray-300 hover:text-gray-700"
                >
                    <ArrowLongLeftIcon class="mr-3 h-5 w-5 text-gray-400" aria-hidden="true" />
                    Précédent
                </span>
            </div>
            <div class="hidden md:-mt-px md:flex">
                <span 
                    v-for="item in generatePagination(page,count)"
                    :class="itemClass(item)"
                    :key="item"
                    @click="changePage(item)"
                >{{ item }}</span>
            </div>
            <div class="-mt-px flex w-0 flex-1 justify-end">
                <span
                    v-if="page < count"
                    @click="changePage(page+1)"
                    class="cursor-pointer inline-flex items-center border-t-2 border-transparent pl-1 pt-4 text-sm font-medium text-gray-500 hover:border-gray-300 hover:text-gray-700"
                >
                    Suivant
                    <ArrowLongRightIcon class="ml-3 h-5 w-5 text-gray-400" aria-hidden="true" />
                </span>
            </div>
        </nav>
    </section>
</template>

<script setup>
import { ArrowLongLeftIcon, ArrowLongRightIcon } from '@heroicons/vue/20/solid'
</script>

<script>
export default {
    data() {},
    props: {
        count: {
            type: Number
        },
        page: {
            type: Number
        }
    },
    methods: {
        itemClass(item) {
            const pointer = isNaN(item) ? '' : 'cursor-pointer';
            if( this.page === item )
                return pointer+" inline-flex items-center border-t-2 border-primary-500 px-4 pt-4 text-sm font-medium text-primary-600";
            else
                return pointer+" inline-flex items-center border-t-2 border-transparent px-4 pt-4 text-sm font-medium text-gray-500 hover:border-gray-300 hover:text-gray-700";
        },
        changePage(val) {
            if( !isNaN(val) ) this.$emit('change', val)
        },
        /**
         * 
         * @param {Int} currentPage 
         * @param {Int} totalPages 
         * 
         * This method is responsible to generate the pagination based on both parameters
         */
        generatePagination(currentPage, totalPages) {
            if( totalPages < currentPage ) {
                console.error('The current page must be below totalPages');
                return [1];
            }

            const pagination = [];
            const steps = 3;

            // Add the first three pages
            for (let page = 1; page <= Math.min(totalPages, steps); page++) {
                pagination.push(page);
            }

            // Add dots if there are more than three pages and the current page is not within the first three
            if (totalPages > steps*2 && currentPage >= steps*2) {
                pagination.push('...');
            }

            // Add the current page
            if ( totalPages > steps * 2 ) {
                if( currentPage - 1 > steps && currentPage - 1 <= totalPages - steps ) pagination.push(currentPage -1);

                if ( currentPage - 1 >= steps && currentPage <= totalPages - steps ) pagination.push(currentPage);

                if ( currentPage < totalPages - steps && currentPage + 1 > steps ) pagination.push(currentPage + 1);
            }


            // Add dots if there are more than three pages and the current page is not within the last three
            if (totalPages > 5 && currentPage < totalPages - 4)
                pagination.push('...');

            // Add the last three pages
            for (let page = totalPages - 2; page <= totalPages; page++) {
                if (page > 3) {
                    pagination.push(page);
                }
            }

            return pagination;
        }
    }
}
</script>
