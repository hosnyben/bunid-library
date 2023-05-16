<template>
    <div class="bg-white items-center justify-between w-full flex rounded-full shadow-lg p-2 max-w-5xl">
        <input
            class="rounded-full w-full py-4 pl-4 text-gray-700 bg-gray-100 leading-tight focus:outline-none focus:shadow-outline lg:text-sm text-xs"
            type="text"
            placeholder="Trouver un livre"
            v-model="currentVal"
            v-on:keyup.enter="clickIt"
        />

        <div class="bg-gray-600 p-2 hover:bg-primary-400 cursor-pointer ml-2 rounded-full" @click="clickIt">
            <svg
                class="w-6 h-6 text-white"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor"
            >
                <path
                    fill-rule="evenodd"
                    d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
                    clip-rule="evenodd"
                />
            </svg>
        </div>
    </div>
</template>
<script setup></script>

<script>
export default {
    data() {
        return {
            currentVal : ''
        }
    },
    props: {
        modelValue : {
            type : String
        }
    },
    watch : {
        modelValue: {
            handler (){
                if( this.currentVal !== this.modelValue ) this.currentVal = this.modelValue;
            }
        },
        currentVal: {
            handler (val) {
                this.$emit('update:modelValue',val);
            }
        },
    },
    computed: {},
    mounted() {
		this.currentVal = this.modelValue;
    },
    methods: {
        clickIt() {
            this.$emit('search');
        }
    }
}
</script>
