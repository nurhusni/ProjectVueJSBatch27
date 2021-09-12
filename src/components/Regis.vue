<template>
  <v-card>
    <v-toolbar dark color="green">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>Registrasi</v-toolbar-title>
    </v-toolbar>
    <v-divider></v-divider>
    <v-container fluid>
      <v-form ref="form">
        <v-text-field v-model="nama" label="nama" required append-icon="mdi-account"></v-text-field>
        <v-text-field v-model="email" label="E-mail" required append-icon="mdi-email"></v-text-field>
        <v-text-field v-model="password" :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'" :type="showPassword ? 'text' : 'password'" label="Password" counter @click:append="showPassword = !showPassword"></v-text-field>

        <div class="text-xs-center">
          <v-btn dark color="green" @click="submit" class="mr-4">
            Registrasi
          </v-btn>

          <v-btn dark color="red" @click="clear" class="mr-4">
            clear
          </v-btn>
        </div>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
import { mapActions } from "vuex";
export default {
  data() {
    return {
      nama: "",
      email: "",
      showPassword: false,
      password: "",
      error : [],
    };
  },

  //token buat alert ntar gua ganti
  methods: {
    ...mapActions({
      setAlert: "alert/set",
    }),

    close() {
      this.$emit("closed", false);
    },

    clear(){
        this.nama = "";
        this.email = "";
        this.password = "";
    },
    Validation(){
    this.error = []

    if(this.nama.length <= 0){
        this.setAlert({
            status: true,
            color: "error",
            text: "nama tidak boleh kosong",
          });
        this.error.push("")
        }

    else if(this.email.length <= 0){
       this.setAlert({
            status: true,
            color: "error",
            text: "Email tidak boleh kosong",
          });
      this.error.push("")
        }

    else if(this.password.length <= 0){
        this.setAlert({
            status: true,
            color: "error",
            text: "Pasword tidak boleh kosong",
          });
        this.error.push("")
        }


    },

    //disini ganti yang regis
    submit() {

    this.Validation()

    if(this.error.length === 0){
        
        let formData = new FormData()
        formData.append('name', this.nama)
        formData.append('email', this.email)
        formData.append('password', this.password)

      const config = {
        method: "post",
        url: "http://demo-api-vue.sanbercloud.com/api/auth/register",
        data: formData
      };
      this.axios(config)
        .then((response) => {
          console.log(response.data)

          this.setAlert({
            status: true,
            color: "success",
            text: "Regis Berhasil",
          });
          this.close();
        })
        .catch((response) => {
          console.log(response);
          this.setAlert({
            status: true,
            color: "error",
            text: "Email Sudah Terdaftar",
          });
        });
    }
    },
  },
};
</script>