<template>
  <div class="app font-monospace">
    <div class="content">
      <AppInfo :allMoviesCount='movies.length' :favouriteMoviesCount="movies.filter(c => c.favourite).length"/>
      <div class="search-panel">
        <SearchPanel />
        <AppFilter />
      </div>
      <MovieList :movies="movies" @onLike="onLikeHandler"/>
      <MovieAddForm @createMovie="createMovie"/>
    </div>
  </div>
</template>



<script>
import AppInfo from "../app-info/AppInfo.vue";
import SearchPanel from "../search-panel/SearchPanel.vue";
import AppFilter from "../app-filter/AppFilter.vue"
import MovieList from "../movie-list/MovieList.vue"
import MovieAddForm from "../movia-add-form/MovieAddForm.vue"

export default {
  components: {
    AppInfo,
    SearchPanel,
    AppFilter,
    MovieList,
    MovieAddForm
  },
  data() {
        return{
            movies:[
                {   
                    name: "Harry Potter",
                    viewers: 811,
                    favourite: false,
                    like: true,
                    id: 1
                },
                {
                    name: "Doctor House",
                    viewers: 483,
                    favourite: true,
                    like: true,
                    id: 2
                },
                {
                    name: "Sherlock Holmes",
                    viewers: 923,
                    favourite: true,
                    like: false,
                    id: 3
                },
            ],
        }
    },
    methods: {
      createMovie(item) {
        this.movies.push(item)
      },
      onLikeHandler(id) {
        const arr = this.movies.map(item => {
          if(item.id == id) {
            item.like = !item.like
          }
          return item 
        })
      },
    }
}
</script>


<style>
.app{
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
</style>