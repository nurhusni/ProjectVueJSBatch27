<template>
  <v-container class="ma-0 pa-0" grid-list-sm>
    <div class="text-right">
      <v-btn small text to="/blogs" class="blue--text">
        All Blogs <v-icon>mdi-chevron-right</v-icon>
      </v-btn>
    </div>

    <v-dialog v-model="formPost" max-width="600px">
      <v-card>
        <div v-if="statusSPAN == 'tambahBlog'">
          <v-card-title>
            <span class="text-h5">Buat Post Baru</span>
          </v-card-title>
        </div>
        <div v-else-if="statusSPAN == 'updateBlog'">
          <v-card-title>
            <span class="text-h5">Edit Post</span>
          </v-card-title>
        </div>

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
        v-on:editPost="editPost($event)"
        v-on:deletePost="deletePost($event)"
      ></blog-item-component>
    </v-layout>
  </v-container>
</template>

<script>
import { mapGetters, mapMutations } from "vuex";
import BlogItemComponent from "../components/BlogItemComponent.vue";
export default {
  data: () => ({
    title: "",
    description: "",
    status: "submit",
    blogId: "",
    formPost: false,
    apiDomain: "https://demo-api-vue.sanbercloud.com/",
    blogs: [],
    statusSPAN: "tambahBlog",
  }),
  components: {
    "blog-item-component": BlogItemComponent,
  },
  computed: {
    ...mapGetters({
      count: "counter/count",
    }),
  },
  methods: {
    go() {
      const config = {
        method: "GET",
        url: this.apiDomain + "api/v2/blog/random/6",
      };
      this.axios(config)
        .then((response) => {
          let { blogs } = response.data;
          this.blogs = blogs;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    ...mapMutations({
      increment: "counter/increment",
    }),

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
      this.statusSPAN = "updateBlog";
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
          this.formPost = false;
          alert("Post diedit");
          this.statusSPAN = "tambahBlog";
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
          this.formPost = false;
          this.statusSPAN = "tambahBlog";
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
