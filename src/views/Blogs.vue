<template>
  <v-container class="ma-0 pa-0" grid-list-sm>
    <v-subheader>All Blogs</v-subheader>

    <v-dialog v-model="formPost" persistent max-width="600px">
      <template v-slot:activator="{ on, attrs }">
        <v-btn @click="clearForm" color="primary" v-bind="attrs" v-on="on">
          Buat Post
        </v-btn>
      </template>

      <v-card>
        <v-card-title>
          <span class="text-h5">Buat Post Baru</span>
        </v-card-title>

        <v-form ref="form">
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    v-model="title"
                    label="Judul"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-textarea
                    v-model="description"
                    label="Isi Post di Sini"
                    required
                  ></v-textarea>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-btn color="red" text @click="formPost = false">
              Batal
            </v-btn>
            <v-btn
              color="blue"
              text
              @click="status == 'submit' ? submitForm() : updatePost(blogId)"
            >
              Simpan
            </v-btn>
          </v-card-actions>
        </v-form>
      </v-card>
    </v-dialog>

    <v-layout wrap>
      <blog-item-component
        v-for="blog in blogs"
        :key="`blog-` + blog.id"
        :blog="blog"
        :edit="true"
        v-on:editPost="editPost($event)"
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
    status: "submit",
    blogId: "",
    formPost: false,
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
      this.status = "submit";
      this.blogId = null;
    },

    editPost(blog) {
      this.title = blog.title;
      this.description = blog.description;
      this.status = "update";
      this.blogId = blog.id;
      this.formPost = true;
    },

    updatePost(blogId) {
      let formData = new FormData();
      formData.append("title", this.title);
      formData.append("description", this.description);

      const config = {
        method: "post",
        url: `${this.apiDomain}/api/blog/${blogId}`,
        params: { _method: "PUT" },
        data: formData,
      };

      this.axios(config)
        .then((response) => {
          this.go();
          console.log(response);
          alert("Post diedit");
        })
        .catch((error) => {
          console.log(error);
        });
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

    submitForm() {
      let formData = new FormData();
      formData.append("title", this.title);
      formData.append("description", this.description);

      const config = {
        method: "post",
        url: `${this.apiDomain}/api/blog`,
        data: formData,
      };

      this.axios(config)
        .then((response) => {
          this.clearForm();
          this.go();
          console.log(response);
          alert("Berhasil");
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
