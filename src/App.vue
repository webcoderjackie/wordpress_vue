<template>
  <title>Wordpress</title>
  <div id="app">
    <div class="banner_wapper">
      <div class="container">
        <div class="banner_content">
          <div class="banner_content_inner">
        <h2>Insights & Resources</h2>
        <p>
          Introductory copy that goes underneath the H1 to better<br />contextualise
          what this does and who it’s for.
        </p>
      </div>
    </div>
      </div>
    </div>

    <div class="main-wapper">
      <div class="container">
        <div class="row">
          <div
            class="col-4 m4 mt-4"
            v-for="(post, index) in posts"
            :key="index"
          >
            <div class="card">
              <div class="box-img">
                <span class="category_title"> {{ post?._embedded['wp:term'][0][0].name }} </span>
                <img
                  v-if="post?._embedded['wp:featuredmedia']"
                  :src="post?._embedded['wp:featuredmedia'][0].source_url"
                />
                <span class="box_date">
                  <strong>{{ getPostDate(post.date) }}  | 5 minute read</strong>
                </span>
                <div class="card-title">
                  <a :href="post.guid.rendered" target="_blank">{{
                    post.title.rendered
                  }}</a>
                </div>
              </div>
              <div class="location" v-html="post.metadata.location[0]"></div>
              <div class="card-content" v-html="post.excerpt.rendered"></div>
              <div class="card-tags" v-if="post?._embedded['wp:term'][1].length>0" ><a v-for="term in post._embedded['wp:term'][1]" :key="term.id">
                   {{ term.name }}
      </a></div>
            </div>
          </div>
          <div class="loadmore_btn_wapper">
            <button class="loadmore_btn" :disabled="disabled" @click="handleClickLoadMore()">
              Load More
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
// import { toRaw } from "vue";
export default {
  data() {
    return {
      // Wordpress Posts Endpoint
      postsUrl: "http://localhost/test_wp_vue/wp-json/wp/v2/posts",
      queryOptions: {
        per_page: 6,
        page: 1,
        _embed: true,
        changeOrigin: true,
      },

      posts: [],
      page: 1,
      disable: false,
    };
  },
  computed: {
    disabled: function () {
      return this.disable;
    },
  },
  methods: {
    getRecentMessages() {
      axios
        .get(this.postsUrl, { params: this.queryOptions })
        .then((response) => {
          if (response) {
            this.posts = response.data;
          }
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getPostDate(date) {
      return moment(date).format("MMM D YYYY");
    },
    handleClickLoadMore() {
      this.page = this.page + 1;
      axios
        .get(this.postsUrl, {
          params: {
            per_page: 6,
            page: this.page,
            _embed: true,
          },
        })
        .then((response) => {          
          if (response.data) {
            response.data.length < 6 ? (this.disable = true) : "";
            response.data.map((i) => {
              this.posts.push(i);
            });
          }
        });
    },
  },
  mounted() {
    this.getRecentMessages();
  },
};
</script>

<style>

@font-face {
  font-family: 'sofia-pro, sans-serif';
  src: url('./assets/fonts/sofiapro-light.otf');
}

.container {
  max-width: 1180px;
  margin: 0 auto;
}

.row {
  display: flex;
  flex-wrap: wrap;
  margin-top: 100px;
}

.col-4 {
    width: 30.5%;
    padding: 0px;
    margin-bottom: 10px;
}

.box-img img {
  max-width: 100%;
  border-radius: 5px;
  margin-bottom: 10px;    
  min-height: 256px;
}
span.box_date {
    color: #00C4CB;
    margin-bottom: 10px;
    display: block;
    font-size: 16px;
    line-height: 26px;
}
.location {
  color: #00C4CB;
    margin-bottom: 10px;
    margin-top: 15px;
    display: block;
    font-size: 16px;
    line-height: unset;

}
.banner_wapper {
  width: 100%;
  height: 300px;
  background: rgb(2,0,36);
background: linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(0,65,114,1) 35%, rgba(0,212,255,1) 100%);
color: #ffffff;
}

.banner_wapper h2 {
  margin: 0;
}

body,
html {
  padding: 0;
  margin: 0;
  font-family: sofia-pro, sans-serif;
}
.box-img {
    position: relative;
}
.card-title a {
    font-size: 20px;
    text-decoration: none;
    color: #004172;
    line-height: 31px;
}
.card-content p {
    font-size: 16px;
    line-height: 26px;
}
span.category_title {
    position: absolute;
    top: 22px;
    left: 27px;
    padding: 7px 10px;
    background: #FE550B;
    border-radius: 5px;
    color: #ffffff;
    font-size: 14px;
}
.card-tags a {
    border: 1px solid #FF4F02;
    margin-right: 17px;
    border-radius: 20px;
    padding: 6px 16px;
    color: #FF4F02;
    text-decoration: none;
}
button:disabled,
button[disabled]{
 display: none;
}
.loadmore_btn_wapper{
  display: flex;
    width: 100%;
}
.loadmore_btn {
    border: 2px solid #FF4F02;
    margin-right: 10px;
    border-radius: 20px;
    padding: 9px 28px;
    color: #FF4F02;
    text-decoration: none;
    background: none;
    margin: 30px auto 50px auto;
    cursor: pointer;
    font-size: 16px;
}
.banner_content{display: table;
    height: 300px;}
.banner_content_inner{
  display: table-cell;
    vertical-align: middle;
}
.banner_content_inner h2{
  font-size: 50px;
  font-weight: normal;
}
.banner_content_inner p{
  font-size: 20px;
  line-height: 28px;
}
.main-wapper .row {
    gap: 50px;
}
</style>
