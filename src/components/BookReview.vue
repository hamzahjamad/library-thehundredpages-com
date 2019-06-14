<template>
  <div>
    {{collections.first_letter}}
    <ul>
      <li v-for="book in collections.posts">
        <a :href="book.link" v-html="book.title"></a>
      </li>
    </ul>
    
  </div>
</template>

<script>
export default {
  name: 'BookReview',
  props: {
    collections: Object
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
