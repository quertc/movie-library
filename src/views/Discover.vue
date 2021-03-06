<template>
  <main class="main">
    <MoviesListTitle
      :title="category"
      subtitle="movies"
      class="main__title"
    />
    <MoviesList>
      <MoviesListItem
        v-for="movie in discoverDataMovies"
        :key="movie.id"
        :movie="movie"
      />
    </MoviesList>
    <MoviesListPagination
      class="main__pagination"
      :class="{ main__pagination_hidden: hidePagination || !discoverData.results }"
      @load-more="loadMoreMovies"
    />
  </main>
</template>

<script>
import MoviesListTitle from '@/components/MoviesListTitle.vue';
import MoviesList from '@/components/MoviesList.vue';
import MoviesListItem from '@/components/MoviesListItem.vue';
import MoviesListPagination from '@/components/MoviesListPagination.vue';
import { mapState, mapMutations, mapActions } from 'vuex';

export default {
  components: {
    MoviesListTitle,
    MoviesList,
    MoviesListItem,
    MoviesListPagination,
  },
  props: {
    category: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      hidePagination: false,
    };
  },
  computed: mapState({
    discoverData: state => state.discover.discoverData,
    discoverDataMovies: state => state.discover.discoverDataMovies,
    discoverCategories: state => state.discover.discoverCategories,
  }),
  async mounted() {
    if (!this.discoverCategories.includes(this.$route.params.category)) {
      this.$router.push('/');
    } else {
      await this.fetchDiscoverData([this.$route.params.category]);

      if (this.discoverData.page >= this.discoverData.total_pages) {
        this.hidePagination = true;
      }
    }
  },
  beforeDestroy() {
    this.clearDiscoverData();
  },
  methods: {
    ...mapMutations(['clearDiscoverData']),
    ...mapActions(['fetchDiscoverData']),
    async loadMoreMovies() {
      await this.fetchDiscoverData([this.$route.params.category, this.discoverData.page + 1]);

      if (this.discoverData.page >= this.discoverData.total_pages) {
        this.hidePagination = true;
      }
    },
  },
};
</script>

<style lang="sass" scoped>
.main
  margin-top: .4rem
  padding: 1.5rem 3.1rem
  &__title
    margin-bottom: 3.6rem
  &__pagination
    margin-top: 1.4rem
    &_hidden
      visibility: hidden
      opacity: 0
</style>
