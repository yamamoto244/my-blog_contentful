<template>
  <div class="main-content">
    <div class="container">
      <h2 class="title is-2">About this website.</h2>
      <p>Homeタブに最新記事一覧を表示しています。Read Moreから詳細記事を表示できます。Recent Postsはコンポーネントにして、分割しています。(components/PostPreciew.vue)</p>
      <br>
      <h4 class="title is-4">ハマったポイント</h4>
      <ul>
        <li>contentful.jsファイルを編集し、保存する際に自動補完機能による影響でエラーが生じ、正しくcontentfulからの取得ができていなかった</li>
        <li>asyncDataメソッドの利用して、contentfulからの取得を適切に行う方法の模索に時間がかかった</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  head: {
    title: "About"
  }
};
</script>
