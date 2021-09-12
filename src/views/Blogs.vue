<template>
  <v-container class="ma-0 pa-0" grid-list-sm>
    <v-subheader>All Blogs</v-subheader>

    <v-btn @click="clearForm" color="primary" dark v-bind="attrs" v-on="on">
      Buat Post
    </v-btn>

    <v-layout wrap>
      <blog-item-component
        v-for="blog in blogs"
        :key="`blog-` + blog.id"
        :blog="blog"
        v-on:deletePost="deletePost($event)"
      ></blog-item-component>
    </v-layout>
    <v-pagination
      v-model="page"
      @input="go"
      :length="lengthPage"
      :total-visible="perPage"
    ></v-pagination>
  </v-container>
</template>

<script>
import BlogItemComponent from "../components/BlogItemComponent.vue";
export default {
  data: () => ({
    title: "",
    description: "",
    photo: "",
    status: "submit",
    idEditBlog: "",
    apiDomain: "https://demo-api-vue.sanbercloud.com",
    blogs: [],
    page: 0,
    lengthPage: 0,
    perPage: 0,
  }),

  components: {
    "blog-item-component": BlogItemComponent,
  },
  methods: {
    go() {
      const config = {
        method: "get",
        url: `${this.apiDomain}/api/v2/blog?page=${this.page}`,
      };
      this.axios(config)
        .then((response) => {
          let { blogs } = response.data;
          this.blogs = blogs.data;
          this.page = blogs.current_page;
          this.lengthPage = blogs.last_page;
          this.perPage = blogs.per_page;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    clearForm() {
      this.title = "";
      this.description = "";
      this.photo = "";
      this.status = "submit";
      this.idEditBlog = null;
    },

    editBlog(blog) {
      this.title = blog.title;
      this.description = blog.description;
      this.status = "update";
      this.idEditBlog = blog.id;
      this.dialog = true;
    },

    deletePost(blogId) {
      const config = {
        method: "post",
        url: `${this.apiDomain}/api/blog/${blogId}`,
        params: { _method: "DELETE" },
      };

      this.axios(config)
        .then((response) => {
          this.go();
          console.log(response);
          alert("Post dihapus");
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  created() {
    this.go();
  },
};
</script>
