<template>
    <div class="app font-monospece">
        <div class="content">
            <AppInfo :allMoviesCount="movies.length" :favouriteMoviesCount="movies.filter( c => c.favourite).length"/>
            <div class="search-panel">
                <SearchPanel :updateTermHandler="updateTermHandler"/><br>
                <AppFilter :updateFilterHandler="updateFilterHandler" :filterName="filter"/>
            </div>
            <div v-if="!movies.length && !isLoading">Kinolar yo'q</div>
            <div v-if="isLoading" >Loading...</div>
            <MovieList 
                v-else
                :movies="onFilterHandler(onSearchHandler(movies, term), filter)" 
                @onToggle="onToggleHandler" 
                @onRemove="onRemoveHandler"
            />
            <nav aria-label="pagination">
                <ul class="pagination pagination-lg">
                    <li class="page-item active" aria-current="page">
                    <span class="page-link">1</span>
                    </li>
                    <li class="page-item"><a class="page-link" href="#">2</a></li>
                    <li class="page-item"><a class="page-link" href="#">3</a></li>
                </ul>
            </nav>
            <MovieAddForm @createMovie="createMovie"/>
        </div>
    </div>
</template>


<script>
import AppInfo from "@/companents/app-info/AppInfo.vue"
import SearchPanel from "@/companents/search-panel/SearchPanel.vue"
import AppFilter from "@/companents/app-filter/AppFilter.vue"
import MovieList from "@/companents/movie-list/MovieList.vue"
import MovieAddForm from "@/companents/movie-add-form/MovieAddForm.vue"
import axios from 'axios'
import PrimaryButton from "@/ui-companents/PrimaryButton.vue"
export default {  
    components: { AppInfo, SearchPanel, AppFilter, MovieList, MovieAddForm, PrimaryButton },
    data() {
        return {
            movies: [],
            term: '',
            filter: 'all',
            isLoading: false,
            limit: 10,
            page: 1,
        }
    },
    methods: {
        async createMovie(item) {
            const response = await axios.post('https://jsonplaceholder.typicode.com/posts', item)
            console.log(response)
            this.movies.push(item)
        },
        onToggleHandler({id,prop}){
            console.log(prop)
            this.movies = this.movies.map(item => {
                if (item.id == id){
                    return {...item, [prop]: !item[prop]}
                }
                return item
            })
        },
        onRemoveHandler(id) {
            this.movies=this.movies.filter(c => c.id !== id)
        },
        onSearchHandler(arr, term) {
            if (term.length == 0){
                return arr
            }

            return arr.filter(c => c.name.toLowerCase().indexOf(term) > -1)
        },
        onFilterHandler(arr, filter){
            switch (filter){
                case "popular":
                    return arr.filter(c => c.like)
                case "mostViewers":
                    return arr.filter(c => c.viewers > 500)
                default:
                    return arr
            }
        },
        updateTermHandler(term) {
            this.term = term
        },
        updateFilterHandler(filter){
            this.filter=filter
        },
        async fetchMovie() {
            try {
                this.isLoading = true
                setTimeout(async () => {
                const {data} = await axios.get("https://jsonplaceholder.typicode.com/posts", {
                    params: {
                        _limit : this.limit,
                        _page : this.page,
                    }
                })
                const newArr = data.map(item => ({
                    id: item.id,
                    name: item.title,
                    like: false,
                    favourite: false,
                    viewers: item.id * 10,  
                }))
                this.movies = newArr
                this.isLoading = false
                }, 3000)
            } catch(error) {
                alert(error.message
                )
            }finally{
                this.isLoading = false
            }
        }
    },
    mounted() {
        this.fetchMovie()
    },
}
</script>

<style>
.app{
    height: 100vh;
    color: #000;
}

.content{
    width: 1000px;
    min-height: 700px;
    background-color: #fff;
    margin: 0 auto;
    padding: 5rem 0;
}
.search-panel{
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: #fcfaf5;
    border-radius: 4px;
    box-shadow: 15px 15px 15px rgba(0, 0, 0, 0.15);
}
</style>