<template>
  <div id="app" class="row">
    <div class="col-12" v-for="post in postWithLink" :key="post.id">
      <BookReview :collections="post"></BookReview>
    </div>
    
  </div>
</template>

<script>
import BookReview from './components/BookReview.vue'
import axios from 'axios'
import _ from 'lodash';

export default {
  name: 'app',
  data() {
    return {
      posts: []
    }
  },
  computed: {
      postWithLink() {
         let postCollection = this.posts.map(p => {
           let temp = {
            id: '',
            title : '',
            link : '',
            first_letter: ''
           };

           temp.id = p.id;
           temp.title = p.title.rendered.replace(/[A-Za-z\W]+\W+[\||:]\W+/g, ""); //remove 'Book Review | ' keyword from title
           temp.link = p.link;
           temp.first_letter = temp.title.substring(0,1);

           return temp;
         }).sort((a, b) => (a.title > b.title) ? 1 : -1);

         return _(postCollection)
                  .groupBy(x => x.first_letter)
                  .map((value, key) => ({first_letter: key, posts: value}))
                  .value();
      } 
  },
  methods: {
     populatePosts() {
       let query = ( ids => {
        ids = JSON.parse(ids);
        let result = "?per_page=100&";
        for (let i = ids.length - 1; i >= 0; i--) {
          result += "categories=" + ids[i]
          if (i !== 0) {
            result += ",";
          }
        }

        return result;
     })(process.env.VUE_APP_CATEGORY_IDS);

     //TODO handle pagination
     axios
      .get(process.env.VUE_APP_WORDPRESS_REST_API+query)
      .then(response => (this.posts = response.data))
     }
  },
  created() {
     document.title = process.env.VUE_APP_TITLE;
     this.populatePosts();  
  }, 
  components: {
    BookReview
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 60px 20px;
}
</style>
