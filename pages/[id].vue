<template>
  <div class="post">
    <div class="post__header">
      <h1>{{ item.title }}</h1>
      <hr class="my-4">
      <div class="post__linkblock">
        <div class="post__links">tags:</div>
        <template v-for="(tag, index) in item.tag">
          <span v-if="index > 0">|,</span>
          <b-link class="post__link"
            :to="{name: 'tag-id', params: { id: tag.id } }"
          >{{ tag.name }}</b-link>
        </template>
        <div class="post__links">|category:</div>
        <b-link class="post__link" :to="{name: 'category-id', params: { id: item.category.id } }"
        >{{ item.category.name }}</b-link>
        <div class="post__time"></div>
      </div>
    </div>
    <div class="post__body">
      <div class="post__body-text" v-html="item.body"></div>
    </div>
    <b-button
      to = "/"
      variant="outline-primary"
    >TOP</b-button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [],
    };
  },
  async asyncData({ params }) {
    const { data } = await fetch(
      `https://${process.env.MICROCMS_SERVICE_DOMAIN}.microcms.io/api/v1/blog/${params.id}`,
      {
        headers: { "X-API-KEY": process.env.MICROCMS_API_KEY },
      }
    );
    return {
      item: data,
    };
  },
};
</script>

<style lang="scss">
.post {
  &__time {
    text-align: right;
  }
  &__linkblock {
    display: flex;
    justify-content: flex-end;
  }
  &__links {
    display: flex;
    justify-content: flex-end;
    margin-left: 1em;
  }
  &__link {
    margin-left: 0.5em;
  }
  &__header {
    background-color: #eee;
  }
  &__header,
  &__body {
    @media (min-width: 992px) {
      padding: 5em;
    }
    @media (max-width: 991px) {
      padding: 2em;
    }
  }
  &__body {
    h2 {
      margin-top: 1em;
      padding-bottom: 0.2em;
      border-bottom: 1px solid #ddd;
    }
    a {
      overflow-wrap: break-word;
    }
    code {
      padding: 0.1em 0.3em;
      margin: 0.1em 0.3em;
      background-color: rgba(#e83e8c, 0.05);
      border-radius: 0.1em;
    }
    pre {
      padding: 0.5em;
      background-color: #ddd;
      border-radius: 0.1em;
      code {
        padding: 0 !important;
        margin: 0 !important;
        background-color: transparent !important;
        border-radius: 0 !important;
      }
    }
    img {
      width: auto;
      max-width: 100%;
    }
  }
}
</style>
  