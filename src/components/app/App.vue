<template>
  <div class="app font-monospace">
    <div class="content">
      <AppInfo :allMoviesCount='movies.length' :favouriteMoviesCount="movies.filter(c => c.favourite).length" />
      <div class="search-panel">
        <SearchPanel :updateTermHandler="updateTermHandler" />
        <AppFilter :updateFilterHandler="updateFilterHandler" :filterName="filter" />
      </div>
      <Box v-if="!movies.length && !isLoading">
        <p class="text-center fs-3 text-danger">Kinolar yo'q</p>
      </Box>
      <Box v-else-if="isLoading">
        <Loader></Loader>
      </Box>
      <MovieList v-else :movies="onFilterHandler(onSearchHandler(movies, term), filter)" @onToggle="onToggleHandler"
        @onRemove="onRemoveHandler" />
      <Box class="d-flex justify-content-center">
        <nav aria-label="pagination">
          <ul class="pagination">
            <li v-for="pageNumber in totalPages" class="page-item" aria-current="page" :class="{'active': pageNumber == page}" :key="pageNumber" @click="pageChangeHandler(pageNumber)">
              <span class="page-link">{{ pageNumber }}</span>
            </li>
          </ul>
        </nav>
      </Box>
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
      filter: 'all',
      isLoading: false, // state - holat
      limit: 10,
      page: 1,
      totalPages: 0
    }
  },
  methods: {
    async createMovie(item) {
      console.log('3123123', item);
      const response = await axios.post('https://jsonplaceholder.typicode.com/posts', item)
      console.log(response)
      this.movies.push(response.data)
    },
    onToggleHandler({ id, prop }) {
      this.movies = this.movies.map(item => {
        if (item.id == id) {
          return { ...item, [prop]: !item[prop] }
        }
        return item
      })
    },
    async onRemoveHandler(id) {
      try {
        const response = await axios.delete(`https://jsonplaceholder.typicode.com/posts/${id}`)
        this.movies = response.data.filter(c => c.id !== id)
      } catch {
        alert(error.message)
      }
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
        this.isLoading = true
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _limit: this.limit,
            _page: this.page
          }
        })
        const newArr = response.data.map(item => ({
          id: item.id,
          name: item.title,
          like: false,
          favourite: false,
          viewers: Math.floor(Math.random() * 1000)
        }))
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.movies = newArr
        console.log(this.totalPages);
      } catch (error) {
        alert(error.message)
      } finally {
        this.isLoading = false
      }
    },
    pageChangeHandler(page) {
      this.page = page
      this.fetchMovie()
    },
  },
  mounted() {
    this.fetchMovie()
  },
  watch: {
    page() {
      this.fetchMovie()
    }
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