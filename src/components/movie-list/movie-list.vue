<template>
  <div class="movie-list">
    <input
      class="movie-list__search"
      type="text"
      placeholder="Search for a movie"
      @change="updateSearchQuery"
    />
    <ul>
      <li
        v-for="(item, index) in filteredMovies"
        :key="index"
        :class="{ active: item.id === activeMovieId }"
        @click="previewMovie(item)"
      >
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { TMovieData } from '@/types'

export default defineComponent({
  name: 'MovieList',

  emits: ['selected-movie'],

  props: {
    movieList: {
      type: Array as () => TMovieData[],
      default: []
    },
    activeMovieId: {
      type: Number,
      default: 0
    }
  },

  data() {
    return {
      searchQuery: ''
    }
  },

  computed: {
    filteredMovies(): TMovieData[] {
      if (!this.searchQuery) return this.movieList

      return this.movieList.filter((movie: TMovieData) => {
        const searchQuery = this.searchQuery.toLowerCase()
        const movieTitle = movie.title.toLowerCase()
        const titleMatchesQuery = movieTitle.includes(searchQuery)

        return titleMatchesQuery
      })
    },

    isActiveMovieOnFilteredList(): boolean {
      return this.filteredMovies.some((movie: TMovieData) => {
        return movie.id === this.activeMovieId
      })
    }
  },

  methods: {
    previewMovie(data: TMovieData) {
      this.$emit('selected-movie', data.id)
    },

    updateSearchQuery(event: Event) {
      const target = event?.target as HTMLInputElement
      this.searchQuery = target?.value as string | ''
    }
  },

  watch: {
    filteredMovies: {
      deep: true,
      handler(movieList: TMovieData[]) {
        if (movieList.length >= 1 && !this.isActiveMovieOnFilteredList) {
          this.previewMovie(movieList[0])
        }
      }
    }
  }
})
</script>

<style lang="scss">
$header-height: 83px;
$input-height: 48px;

.movie-list {
  position: fixed;
  top: $header-height;
  left: 0;
  height: calc(100% - $header-height);
  width: 25%;
  background-color: white;
  border-right: 1px solid black;
  color: black;

  &__search {
    width: 100%;
    padding: 15px 30px;
    box-sizing: border-box;
    border: none;
    border-radius: none;
    font-size: 16px;
    background: rgba(blue, 0.1);
    color: rgba(blue, 0.5);

    &::placeholder {
      color: rgba(blue, 0.5);
    }

    &:focus {
      outline: none;
      background: rgba(blue, 0.05);
    }
  }

  ul {
    position: relative;
    height: calc(100% - $input-height);
    overflow: hidden;
    overflow-y: auto;
    margin: 0;
    padding: 0;
    border-top: 1px solid rgba(black, 1);
  }

  ul li {
    padding: 15px 30px;
    border-bottom: 1px solid rgba(black, 1);
    text-align: left;
    cursor: pointer;

    &:hover {
      background: rgba(black, 0.05);
    }

    &.active {
      background: rgba(blue, 0.5);
      color: white;
    }
  }
}
</style>
