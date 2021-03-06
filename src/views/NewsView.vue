<template>
  <section class="content-page card">
    <spinner class="spinner" v-if="!loaded" key="spinner"></spinner>
    <div class="news-view" >
      <transition name="fade" mode="out-in" v-if="loaded">
        <div>
          <figure class="news-header" :style="{ 'background-image': 'url(' + article.image + ')' }">
            <div class="tags">
              <router-link
                v-for="tag in article.tags"
                class="tags button-small"
                v-if="tag.name != ''"
                :key="tag.name"
                :to="tag.tag.toLowerCase()">{{tag.name}}</router-link>
            </div>
          </figure>
          <div class="content-container">
            <h1>{{article.title}}</h1>
            <h2>{{dateFix(article.date)}}</h2>
            <article v-html="article.body" class="body"></article>
            <p>{{newsContactInfo.display_name}} |
              {{newsContactInfo.phone}} |
              <a :href="`mailto:${newsContactInfo.email}`">{{newsContactInfo.email}}</a>
            </p>
          </div>
        </div>
      </transition>
    </div>
  </section>
</template>

<script>
import Spinner from '../components/Spinner.vue'
import { router } from '../app'
import format from 'date-fns/format'

export default {
  name: 'news-view',

  components: {
    Spinner
  },

  computed: {
    loaded() {
      if(this.$store.state.news.error.length > 0) {
        router.replace('/404');
      }
      
      let article = this.$route.params.article
      for (var uid in this.$store.state.news.articles)
        if(uid === article) return true
    },
    article(){
      let article = this.$route.params.article
      for (var uid in this.$store.state.news.articles)
        if(uid === article) return this.cleanHTML(this.$store.state.news.articles[uid])
    },
    newsContactInfo() {
      return this.$store.state.news.newsContact;
    }
  },

  methods: {
    cleanHTML: function(data){
      return data
    },

    tagFilter: (tag) => {
      if(tag.includes('_')) {
        return '/directory/' + tag
      }else{
        return '/departments/' + tag.toLocaleLowerCase()
      }
    },

    dateFix (arg) {
      return format(arg, 'MMM D, YYYY')
    }
  },

  asyncData ({ store, route }) {
    return store.dispatch('GET_NEWS_ARTICLE', route.params.article)
  },

}
</script>

<style lang="scss" scoped>
@import '../assets/scss/vars';

.tags{
  margin-bottom: $base-line-height / 4;
}
.news-header {
  min-height: $base-line-height * 12;
  left: -$base-line-height * 2;
  top: -$base-line-height * 2;
  margin-bottom: $base-line-height;
  background-size: cover;
  background-position: center;
  width: calc(100% + #{$base-line-height} * 4);
  margin: 0;
  position: relative;
  display: flex;
  padding-left: $base-line-height * 2;
  align-items: flex-end;
  @include breakpoint-max(desktop) {
    left: -$base-line-height;
    top: -$base-line-height;
    padding-left: $base-line-height;
    width: calc(100% + #{$base-line-height} * 2);
  }
  @include breakpoint-max(tablet){
    min-height: $base-line-height * 8;
  }
}

</style>
