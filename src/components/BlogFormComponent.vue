<template>
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
</template>

<script>
export default {
  data: () => ({}),

  props: [],
};
</script>
