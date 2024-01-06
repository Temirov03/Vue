<template>
    <div class="btn-group">
        <button v-for="btn in filterButtons" :key="btn.name" class="btn" :class='[filterName == btn.name ? "btn-dark":"btn-outline-dark"]' @click="filterHandler(btn.name)">
            {{ btn.title }}
        </button>
       <!-- <button class="btn" type="button" @click="filterHandler('all')" :class="[filterName == 'all' ? 'btn-dark': 'btn-outline-dark']">Barcha kinolar</button>
       <button class="btn" type="button" @click="filterHandler('popular')" :class="[filterName == 'popular' ? 'btn-dark': 'btn-outline-dark']">Mashxur kinolar</button>
       <button class="btn" type="button" @click="filterHandler('mostViewers')" :class="[filterName == 'mostViewers' ? 'btn-dark': 'btn-outline-dark']">Eng ko'p ko'rilgan kinolar</button> -->
    </div>
</template>
<script>
export default {
    props: {
        updateFilterHandler: {
            type: Function,
            required: true,
        },
        filterName: {
            type: String,
            required: true,
        }
    },
    data() {
        return {
            filterButtons:[
                {
                    title: 'Barcha kinolar',
                    name: 'all'
                },
                {
                    title: 'Mashxur kinolar',
                    name: 'popular'
                },
                {
                    title: "Eng ko'p ko'rilgan kinolar",
                    name: 'mostViewers'
                },
            ],
            filter: 'all'
        }
    },
    methods: {
        filterHandler(filter){
            this.filter=filter
            this.updateFilterHandler(this.filter)
        }
    }
}
</script>

<style scoped>
    
</style>