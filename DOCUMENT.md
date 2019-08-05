#　 Nuxt.js + Contentful + Netlify で Blog 調査

公開デモ:https://naughty-hoover-11f0f6.netlify.com/
※事前にこちらで登録を行ってください。
https://www.contentful.com/

#　 Nuxt アプリの作成

```
$npm install -g vue-cli
$vue init nuxt/starter my-blog
? Project name my-blog
? Project description Nuxt.js project
? Author Yamamoto Tsuyoshi
$ cd my-blog/
$ npm install
$npm run dev

```

ローカル環境で確認

#　 contentful-cli のインストール(必要なパッケージの追加)

```
$npm i -g contentful-cli
$contentful login #ウェブブラウザで開くので、ログインしてください
$contentful space create --name 'my-blog'
$contentful space use
$contentful space seed --template blog
$npm install --save contentful
$npm install --save vue-markdown
$npm install --save-dev push-dir

```

また、API トークンを取得する必要があります。Web ブラウザ上で contentful にログインして、先ほど作ったスペースを選択します。
すると Top に「Fetch your content」というメニューが表示されているはずです。「Use the API」をクリックしてください。
そしてそこにある・Space ID・Content Delivery API - access token の二つを忘れないように保存しておいてください。

# Nuxt のアプリケーションのベースの大枠を作る

- ページレイアウトの操作

```
<!-- ./layouts/default.vue -->

<template>
  <div>
    <!-- navigation -->
    <nav class="nav has-shadow">
      <div class="container">
        <div class="nav-left">
          <nuxt-link to="/" class="nav-item">Awesome JS SSR Blog!</nuxt-link>
          <nuxt-link active-class="is-active" to="/" class="nav-item is-tab" exact>Home</nuxt-link>
          <nuxt-link active-class="is-active" to="/about" class="nav-item is-tab" exact>About</nuxt-link>
        </div>
      </div>
    </nav>
    <!-- /navigation -->
    <nuxt/>
  </div>
</template>

<style>
html {
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}

.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
</style>
```

- ブログの TOP ページを作成する

```
<!-- ./pages/index.vue -->
<template>
  <div>
    <section class="hero is-medium is-primary is-bold">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            Welcome to the JavaScript SSR Blog.
          </h1>
          <h2 class="subtitle">
            Hope you find something you like.
          </h2>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
  export default {
    head: {
      title: 'Home'
    }
  }
</script>
```

[補足]
Nuxt.js では、pages ディレクトリ内に単一ファイルコンポーネントとしてページが作成されます。ディレクトリ内にあるすべての.vue ファイルをアプリケーションのルートに自動的に変換します。

- About ページの作成

```
<!-- ./pages/about.vue -->
<template>
  <div class="main-content">
    <div class="container">
      <h2 class="title is-2">About this website.</h2>
      <p>Curabitur accumsan turpis pharetra <strong>augue tincidunt</strong> blandit. Quisque condimentum maximus mi, sit amet commodo arcu rutrum id. Proin pretium urna vel cursus venenatis. Suspendisse potenti. Etiam mattis sem rhoncus lacus dapibus facilisis. Donec at dignissim dui. Ut et neque nisl.</p>
      <br>
      <h4 class="title is-4">What we hope to achieve:</h4>
      <ul>
        <li>In fermentum leo eu lectus mollis, quis dictum mi aliquet.</li>
        <li>Morbi eu nulla lobortis, lobortis est in, fringilla felis.</li>
        <li>Aliquam nec felis in sapien venenatis viverra fermentum nec lectus.</li>
        <li>Ut non enim metus.</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  head: {
    title: 'About'
  }
}
</script>
```

# Contentful から記事を取得する（最新記事一覧ページの作成）

```
$npm install --save contentful
```

plugin ディレクトリの配下に contentful.js を作成して以下のように記載してください。

```
<!-- ./plugin/contentful.js -->
const contentful = require('contentful');
// use default environment config for convenience
// these will be set via `env` property in nuxt.config.js
const config = {
  space: process.env.CTF_SPACE_ID,
  accessToken: process.env.CTF_CDA_ACCESS_TOKEN
};

// export `createClient` to use it in page components
module.exports = {
  createClient() {
    return contentful.createClient(config);
  }
};
```

特にここでハマりました。Qita 記事の通りにコードを持ってきて、貼り付けると Developer Tool でエラーとなりました。
エラーとはなりますが、ページは表示されます。しかし、後の詳細記事ページの作成の際にうまく行きませんでした。
（原因が分かりませんでしたが...m(\_)m）ファイル保存時の自動補完機能でうまく行きました。
おそらく、最後の行に改行がなかったのかもしれません。

また、アプリケーションのベース URL を設定した際に、エラーが出たので、今回は省略しています。時間が足りず、ルーティング設計は自動で任せてしまってます。

```
export default {
  router: {
    base: '/app/'
  }
}
```

続いては API トークンなどを書き込む設定ファイルを作成します。

```

<!-- ./.contentful.json -->
{
  "CTF_PERSON_ID": "15jwOBqpxqSAOy2eOO4S0m",
  "CTF_BLOG_POST_TYPE_ID": "blogPost",
  "CTF_SPACE_ID": "先ほど取得したもの",
  "CTF_CDA_ACCESS_TOKEN": "先ほど取得したもの"
}
```

続いて nuxt.config.js に以下のコードを追加します。

```

<!-- ./nuxt.config.js -->
const config = require('./.contentful.json');

module.exports = {
  /*
   * Headers of the page
   */
  head: {
    titleTemplate: '%s | Awesome JS SSR Blog',
    // ...
    link: [
      // ...
      {
        rel: 'stylesheet',
        href:
          'https://cdnjs.cloudflare.com/ajax/libs/bulma/0.4.2/css/bulma.min.css'
      }
    ]
  },
  // ...
  env: {
    CTF_SPACE_ID: config.CTF_SPACE_ID,
    CTF_CDA_ACCESS_TOKEN: config.CTF_CDA_ACCESS_TOKEN,
    CTF_PERSON_ID: config.CTF_PERSON_ID,
    CTF_BLOG_POST_TYPE_ID: config.CTF_BLOG_POST_TYPE_ID
  }
  // ...
};
```

最後に index.vue に以下を追加して、API で取得したデータを画面上に表示させます。
最新記事一覧を TOP ページに配置するコンポーネント（components/PostPreview.vue）をつくりました

```

<!-- ./pages/index.vue -->
<template>
  <div>
    <section class="hero is-medium is-primary is-bold">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">Welcome to the JavaScript SSR Blog.</h1>
          <h2 class="subtitle">Hope you find something you like.</h2>
          <h1>{{ person.fields.name }}</h1>
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
  asyncData({ env }) {
    return Promise.all([
      client.getEntries({
        "sys.id": env.CTF_PERSON_ID
      }),
      client.getEntries({
        content_type: env.CTF_BLOG_POST_TYPE_ID,
        order: "-sys.createdAt"
      })
    ])
      .then(([entries, posts]) => {
        return {
          person: entries.items[0],
          posts: posts.items
        };
      })
      .catch(console.error);
  }
};
</script>
```

```

<!-- ./components/PostPreview.vue -->
<template>
  <section class="section container">
    <h1 class="title has-text-centered">Recent Posts</h1>
    <div class="columns is-multiline">
      <div class="column is-half" v-for="post in posts" :key="post.id">
        <div class="card">
          <header class="card-header">
            <p class="card-header-title">{{ post.fields.title }}</p>
          </header>
          <div class="card-content">
            <div class="content">
              {{ post.fields.description }}
              <br>
              <small>{{ ( new Date(post.fields.publishDate)).toDateString() }}</small>
            </div>
          </div>
          <footer class="card-footer">
            <nuxt-link
              :to="{ name: 'slug', params: { slug: post.fields.slug }}"
              class="card-footer-item"
            >Read More</nuxt-link>
          </footer>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: ["posts"]
};
</script>
```

# 詳細記事ページの作成

\_slug.vue ファイルの作成

```
<!-- ./pages/_slug.vue -->
<template>
  <section>
    <header class="header">
      <img
        v-if="currentPost.fields.heroImage"
        :src="currentPost.fields.heroImage.fields.file.url + '?fit=scale&w=350&h=196'"
        :srcset="`${currentPost.fields.heroImage.fields.file.url}?w=350&h=87&fit=fill 350w, ${currentPost.fields.heroImage.fields.file.url}?w=1000&h=250&fit=fill 1000w, ${currentPost.fields.heroImage.fields.file.url}?w=2000&h=500&fit=fill 2000w`"
        size="100vw"
        :alt="currentPost.fields.heroImage.fields.description"
      >
    </header>
    <article class="section">
      <div class="headline">
        <h1 class="title has-text-centered">{{ currentPost.fields.title }}</h1>
        <p
          class="headline__date has-text-right"
        >{{ ( new Date(currentPost.fields.publishDate)).toDateString() }}</p>
      </div>
      <vue-markdown class="content">{{ currentPost.fields.body }}</vue-markdown>
      <nav class="pagination is-centered" role="navigation" aria-label="pagination">
        <nuxt-link
          v-if="prevPost"
          class="pagination-previous"
          :to="prevPost.fields.slug"
        >&laquo; {{ prevPost.fields.title }}</nuxt-link>
        <nuxt-link
          v-if="nextPost"
          class="pagination-next"
          :to="nextPost.fields.slug"
        >{{ nextPost.fields.title }} &raquo;</nuxt-link>
      </nav>
    </article>
  </section>
</template>

<script>
import VueMarkdown from "vue-markdown";
import { createClient } from "~/plugins/contentful.js";
const client = createClient();
export default {
  head() {
    return {
      title: this.currentPost.fields.title,
      meta: [
        {
          hid: "description",
          name: "description",
          content: this.currentPost.fields.description
        }
      ]
    };
  },
  data() {
    return {
      allPosts: [],
      currentPost: []
    };
  },
  components: {
    VueMarkdown
  },
  asyncData({ env, params }) {
    return client
      .getEntries({
        content_type: env.CTF_BLOG_POST_TYPE_ID,
        order: "-fields.publishDate"
      })
      .then(entries => {
        const posts = entries.items;
        const current = posts.filter(function(item) {
          return item.fields.slug === params.slug;
        });
        return {
          allPosts: posts,
          currentPost: current[0]
        };
      })
      .catch(console.error);
  },
  computed: {
    dateOrder: function() {
      for (let i = 0; i < this.allPosts.length; i++) {
        if (
          this.allPosts[i].fields.publishDate ===
          this.currentPost.fields.publishDate
        ) {
          return i;
        }
      }
    },
    nextPost: function() {
      if (this.dateOrder === 0) {
        return false;
      } else {
        return this.allPosts[this.dateOrder - 1];
      }
    },
    prevPost: function() {
      if (this.dateOrder === this.allPosts.length - 1) {
        return false;
      } else {
        return this.allPosts[this.dateOrder + 1];
      }
    }
  }
};
</script>

<style scoped>
.headline {
  margin-bottom: 1.5rem;
}
.headline__date {
  font-size: 0.8rem;
}
.content {
  margin-bottom: 3rem;
}
</style>
```

# Netlify へ配置

静的に生成されたサイトの場合
ブランチをデプロイする： master もしくはデプロイしたいブランチ
ビルドコマンド： npm run generate
公開ディレクトリ： dist

Contentful と Netlify の Webhook 連携

# (補足)記事を追加する

contentful にログインし、上部の content タブをクリックした後、右上にある、「Add entry」から追加できます。

# References

https://ja.nuxtjs.org/
https://qiita.com/shiei_kawa/items/a69d16bb389e4b74be5e
https://qiita.com/hisako135/items/082115b50df92ef3e941
https://www.webprofessional.jp/nuxt-js-universal-vue-js/
