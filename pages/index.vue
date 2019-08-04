<template>
  <div>
    <section class="hero is-medium is-primary is-bold">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">Welcome to the JavaScript SSR Blog.</h1>
          <h2 class="subtitle">Hope you find something you like.</h2>
        </div>
      </div>
    </section>
    <PostPreview :posts="posts"></PostPreview>
  </div>
</template>

<script>
import PostPreview from "~/components/PostPreview.vue";
import { createClient } from "~/plugins/contentful.js";

const client = createClient();

export default {
  head: {
    title: "Home"
  },
  components: {
    PostPreview
  },
  data() {
    return {
      posts: []
    };
  },
  // asyncData({ env }) {
  //   return Promise.all([
  //     client.getEntries({
  //       "sys.id": env.CTF_PERSON_ID
  //     }),
  //     client.getEntries({
  //       content_type: env.CTF_BLOG_POST_TYPE_ID,
  //       order: "-sys.createdAt"
  //     })
  //   ])
  //     .then(([entries, posts]) => {
  //       return {
  //         person: entries.items[0],
  //         posts: posts.items
  //       };
  //     })
  //     .catch(console.error);
  // }
  asyncData({ env }) {
    return client
      .getEntries({
        content_type: env.CTF_BLOG_POST_TYPE_ID,
        order: "-fields.publishDate",
        limit: 4
      })
      .then(entries => {
        return {
          posts: entries.items
        };
      })
      .catch(console.error);
  }
};
</script>
