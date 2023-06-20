<template>
  <div class="app font-monospace">
    <div class="content">
      <AppInfo :allMoviesCount='movies.length' :favouriteMoviesCount="movies.filter(c => c.favourite).length" />
      <div class="search-panel">
        <SearchPanel :updateTermHandler="updateTermHandler" />
        <AppFilter :updateFilterHandler="updateFilterHandler" :filterName="filter" />
      </div>
      <MovieList :movies="onFilterHandler(onSearchHandler(movies, term), filter)" @onToggle="onToggleHandler"
        @onRemove="onRemoveHandler" />
      <MovieAddForm @createMovie="createMovie" />
    </div>
  </div>
</template>



<script>
import AppInfo from "../app-info/AppInfo.vue";
import SearchPanel from "../search-panel/SearchPanel.vue";
import AppFilter from "../app-filter/AppFilter.vue"
import MovieList from "../movie-list/MovieList.vue"
import MovieAddForm from "../movia-add-form/MovieAddForm.vue"
import axios from "axios"

export default {
  components: {
    AppInfo,
    SearchPanel,
    AppFilter,
    MovieList,
    MovieAddForm
  },
  data() {
    return {
      movies: [],
      term: '',
      filter: 'all'
    }
  },
  methods: {
    createMovie(item) {
      this.movies.push(item)
    },
    onToggleHandler({ id, prop }) {
      this.movies = this.movies.map(item => {
        if (item.id == id) {
          return { ...item, [prop]: !item[prop] }
        }
        return item
      })
    },
    onRemoveHandler(id) {
      this.movies = this.movies.filter(c => c.id !== id)
    },
    onSearchHandler(arr, term) {
      if (term.length == 0) {
        return arr
      }

      return arr.filter(c => c.name.toLowerCase().indexOf(term) > -1)
    },
    onFilterHandler(arr, filter) {
      switch (filter) {
        case 'popular':
          return arr.filter(c => c.like)
        case 'mostViewers':
          return arr.filter(c => c.viewers > 500)
        default:
          return arr
      }
    },
    updateFilterHandler(filter) {
      this.filter = filter
    },
    updateTermHandler(term) {
      this.term = term
    },
    async fetchMovie() {
      try {
        const {data} = await axios.get('https://jsonplaceholder.typicode.com/posts')
        const newArr = data.map(item => ({
          id: item.id,
          name: item.title,
          like: false,
          favourite: false,
          viewers: Math.floor(Math.random()*1000)
        }))
        this.movies = newArr
      }catch (error) {
        alert(error.message)
      }
    },
  },
  mounted() {
    this.fetchMovie()
  }
}
</script>


<style>
.app {
  height: 100vh;
  color: #000;
}

.content {
  width: 1000px;
  min-height: 700px;
  background-color: #fff;
  margin: 0 auto;
  padding: 5rem 0;
}

.search-panel {
  margin-top: 2rem;
  padding: 1.5rem;
  background-color: #fcfaf5;
  border-radius: 4px;
  box-shadow: 15px 15px 15px rgba(0, 0, 0, 0.15);
}

.none {
  display: none;
}
</style>