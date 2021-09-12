<template>
  <!-- App.vue -->
  <v-app>
    <Alert />
    <Dialog />
    <!-- <v-snackbar
      v-model="snackbarStatus"
      color="success"
      buttom
      timeout="2000"
      multi-line
      outlined
    >
      {{ snackbarText }}

      <template v-slot:action="{ attrs }">
        <v-bt 
          color="pink"
          text
          v-bind="attrs"
          @click="snackbarStatus = false"
        >
          Close
        </v-bt>
      </template>
    </v-snackbar> -->

    <v-navigation-drawer app v-model="drawer" color="grey lighten-1">
      <v-list>
        <v-list-item v-if="!guest">
          <v-list-item-avatar>
            <v-img>
              <img
                :src="
                  user.photo_profile
                    ? apiDomain + user.photo_profile
                    : 'https://lh3.googleusercontent.com/rrxn_tEbbl84mR8ZnGiFXQfR5yvIZowRSjrNzaCf-kXTzsyxoDdNPYr2yNQSDx4YH5osnvF9KkQk9RBzMqMmgwfMCHXh8mc69sAYJM3NoFikVF48VG7p9c4dKQkQs22hldtldJ5U5M0J-8btuWhn9wWXrgtguNZUqbD6rncA8kNr19uWALGjYXgrMwVLe7bn9FKwDf1mPo9y-ZEzRdrFAM1r0Jw2l0ejra7AKXVx-vY6vuwKUQHGXD90R1DBrJ_vYwxvKgqTLcEbSMGEXRjblnpNQN9-BhJhOp5NhDDV84cPZf4p2NasbbpDo-e4rsbbJ3d98UcA5LLuNPXly5bYiJvX-95Z3SwYfov8iDZFKH5EAK2PDFC8VGv73DN30E8PmZvuFm7jLG-kM7hKgR3MEBKGbu9vxXTahzjHSMGA6McqIYw_CoHAqWk4dw5Hrc5r3yCmar7xEc8V1ZV_X93sKGXFEL0YKmy72wu46QZ1z_NOHxuGSqPCALJ2myjZMGamjhT6yWyLicRxUPjTgHcFWL_dWh4B5NANq9F8F1FJCzCzVMykaTQsp1ncHkMphdvPkjc4CMDS2enOrUQRvUtNcs2LvZnf4zHTREJmQhtl-4aZlg0D5aju_WXdNljy2NPVQBSF7Wl2snHYeIIsUq7k085nvwlfJb-WhwzJLMQGwuKHeLZQdxE3NoVvkW84tQZMouKhs5tviEClWTUUbhwX5A=w800-h450-no?authuser=2'
                "
            /></v-img>
          </v-list-item-avatar>
          <v-list-item-content>
            <v-list-item-title>{{ user.name }}r</v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <div class="pa-2" v-if="guest">
          <v-btn block color="primary" class="mb-1" @click="login">
            <v-icon left>mdi-lock</v-icon>
            Login
          </v-btn>
          <v-btn block color="success" @click="regis">
            <v-icon left>mdi-account</v-icon>
            Register
          </v-btn>
          <!-- <v-btn block color="success">
            <v-icon left>mdi-write</v-icon>
            Create a New Blog
          </v-btn> -->
        </div>

        <v-divider></v-divider>

        <v-list-item
          v-for="(item, index) in menus"
          :key="`menu-${index}`"
          :to="item.route"
        >
          <v-list-item-icon>
            <v-icon left class="white--text">{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title class="white--text">{{
              item.title
            }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>

      <template v-slot:append v-if="!guest">
        <div class="pa-2">
          <v-btn block color="red" dark @click="logout">
            <v-icon left>mdi-lock</v-icon>
            Logout
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <v-app-bar app color="red" dark>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>Blogs Project</v-toolbar-title>

      <!-- Pemisah konten -->
      <v-spacer></v-spacer>
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-main>
      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <v-slide-y-transition>
          <!-- If using vue-router -->
          <router-view></router-view>
        </v-slide-y-transition>
      </v-container>
    </v-main>

    <v-footer app>
      Roland Brilianto | Panji Ahmad Nurhusni | Asep Solahuddin
    </v-footer>
  </v-app>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import Alert from "./components/Alert.vue";
import Dialog from "./components/Dialog.vue";

export default {
  components: { Alert, Dialog },
  name: "App",

  data: () => ({
    drawer: false,
    menus: [
      { title: "Home", icon: "mdi-home", route: "/" },
      { title: "Blogs", icon: "mdi-note", route: "/blogs" },
    ],
    apiDomain: "https://demo-api-vue.sanbercloud.com",
    //   guest : true,
    //   snackbarStatus: false,
    //   snackbarText: 'Anda berhasil login'
  }),

  computed: {
    ...mapGetters({
      guest: "auth/guest",
      user: "auth/user",
      token: "auth/token",
    }),
  },

  methods: {
    logout() {
      let config = {
        method: "post",
        url: this.apiDomain + "/api/v2/auth/logout",
        headers: {
          Authorization: "Bearer " + this.token,
        },
      };
      this.axios(config)
        .then(() => {
          this.setToken("");
          this.setUser({});

          // this.guest = true
          this.setAlert({
            status: true,
            color: "success",
            text: "Anda berhasil Logout",
          });
        })
        .catch(() => {
          this.setAlert({
            status: true,
            color: "error",
            text: "Anda gagal Logout",
          });
          // console.log(response)
        });
    },
    login() {
      this.setDialogComponent({ component: "login" });
    },
    regis() {
      this.setDialogComponent({ component: "regis" });
    },
    ...mapActions({
      setAlert: "alert/set",
      setDialogComponent: "dialog/setComponent",
      setUser: "auth/setUser",
      setToken: "auth/setToken",
      checkToken: "auth/checkToken",
    }),
  },
  mounted() {
    if (this.token) {
      this.checkToken(this.token);
    }
  },
};
</script>
