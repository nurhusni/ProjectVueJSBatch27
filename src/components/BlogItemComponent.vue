<template>
  <v-flex xs6>
    <v-card :to="'/blog/' + blog.id" class="mx-auto" max-width="500" outlined>
      <v-list-item three-line>
        <v-list-item-content>
          <div class="text-overline mb-4"></div>
          <v-list-item-title class="text-h5 mb-1" v-text="blog.title">
          </v-list-item-title>
          <v-list-item-subtitle>{{ blog.description }}</v-list-item-subtitle>
        </v-list-item-content>

        <v-list-item-avatar tile size="80" color="grey"
          ><v-img
            :src="
              blog.photo
                ? apiDomain + blog.photo
                : 'https://picsum.photos/200/300'
            "
            class="white--text"
          >
          </v-img>
        </v-list-item-avatar>
      </v-list-item>

      <v-card-actions>
        <v-btn outlined rounded text>
          Baca
        </v-btn>
        <v-btn rounded color="error" @click="$emit('deletePost', blog.id)">
          Hapus
        </v-btn>
      </v-card-actions>
    </v-card>

    <!--  <v-card :to="'/blog/' + blog.id">
      <v-img :src="blog.photo ? apiDomain + blog.photo : 'https://picsum.photos/200/300'" class="white--text" height="200px">
        <v-card-title class="fill-height align-end" v-text="blog.title"> </v-card-title>
      </v-img>

      <v-card-actions>
        <v-progress-linear color="blue-grey" height="7"> </v-progress-linear>
      </v-card-actions>

      <v-card-actions>
        <span>{{ blog.title.substring(0, 15) }}...</span>
      </v-card-actions>
    </v-card>
    -->
  </v-flex>
</template>

<script>
export default {
  data: () => ({
    apiDomain: "http://demo-api-vue.sanbercloud.com/",
  }),

  props: ["blog"],
};
</script>
