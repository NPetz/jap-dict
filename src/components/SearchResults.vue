<template>
  <ul>
    <li v-for="(entry, index) in prop" :key="index"> <a :href="entry.formattedUrl" target="__blank">{{entry.htmlTitle}}</a> </li>
  </ul>
</template>


<script>
export default {
  name: "SearchResults",
  props: ["prop"],
};
</script>
