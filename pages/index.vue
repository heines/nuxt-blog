<template>
<div>
  <b-jumbotron header="しょんぼり技術ブログ" lead="Nuxt.js for Micro CMS" header-level="4" bg-variant="secondary" text-variant="light" fluid="fluid">
    <hr class="my-4" />
    <p>フロントエンドに関することを幅広く書きます。時々雑記。</p>
  </b-jumbotron>
  <div class="container">
    <b-card-group card-deck="card-deck">
      <div v-for="(item, index) in items">
        <b-link class="p-3" :to="item.id" v-show="index &lt; count">
          <b-card :title="item.title">
          </b-card>
        </b-link>
      </div>
    </b-card-group>
    <b-button class="btn-block" @click="addItems" variant="outline-primary" :class="{ 'is-disabled': isDisabled }">MORE</b-button>
  </div>
</div>
</template>
  
<script>
import _sortBy from "lodash/sortBy";
const ADD_ITEMS = 10;
const FIRST_ITEMS = 10;
export default {
  data() {
    return {
      items: [],
      catList: [],
      tagList: [],
    };
  },
  async asyncData() {
    const { tmp } = await fetch(
      `https://${process.env.MICROCMS_SERVICE_DOMAIN}.microcms.io/api/v1/blog?limit=1000`,
      {
        headers: { "X-API-KEY": process.env.MICROCMS_API_KEY },
      }
    );
    console.log(tmp);
    const data = await tmp.json();
    const items = data.contents;
    const categories = items.map((x) => {
      return {
        id: x.category.id,
        name: x.category.name,
      };
    });
    const catList = Array.from(
      new Map(categories.map((cat) => [cat.id, cat])).values()
    );
    const tags = items.map((x) => {
      return x.tag.map((y) => {
        return {
          id: y.id,
          name: y.name,
        };
      });
    });
    const tagList = Array.from(
      new Map(tags.flat().map((tag) => [tag.id, tag])).values()
    ).flat();
    return {
      items: items,
      catList: catList,
      tagList: _sortBy(tagList, "id"),
      total: data.contents.length,
      count: FIRST_ITEMS,
    };
  },
  computed: {
    isDisabled() {
      return this.count >= this.total;
    },
  },
  methods: {
    addItems() {
      this.count += ADD_ITEMS;
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  padding-right: 1.5rem;
  padding-left: 1.5rem;
  padding-bottom: 1.5rem;
}
.title {
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.jumbotron {
  position: relative;
  margin-bottom: 0;
  padding-bottom: 0;
  &::after {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    content: "";
    background-image: url("~@/assets/hero.png");
    background-position: right top;
    background-repeat: no-repeat;
    @media (min-width: 992px) {
      background-size: contain;
    }
    @media (max-width: 991px) {
      opacity: 0.3;
      background-size: auto 50%;
    }
  }
  & .display-4 {
    font-family: "Mochiy Pop P One", sans-serif !important;
  }
}

.btn {
  @media (min-width: 576px) {
    margin-right: 1em;
    margin-left: 1em;
  }
  &.is-disabled {
    opacity: 0;
    pointer-events: none;
  }
}
</style>
  