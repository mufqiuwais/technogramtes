<template>
  <v-card
    height="100%"
    width="80%"

  >
    <v-navigation-drawer
      permanent
      left
      color="#3c3d40"
      class="pa-2"
      width="100%"
    >
      <template v-slot:prepend>
        <v-list-item two-line>
          <img src="../../../assets/technogram-creator-w.png">
        </v-list-item>
      </template>

      <v-divider></v-divider>
      
      <!-- Home Button -->
      <v-list-group
        :value="false"
        color="white"
        active-class="grey darken-4"
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item-content>
        </template>
      </v-list-group>
      
      <!-- Berita Button Group -->
      <v-list-group
        :value="false"
        color="white"
        active-class="grey darken-4"
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>Berita</v-list-item-title>
          </v-list-item-content>
        </template>

        <v-list-item
          v-for="menu in beritas"
          :key="menu.text"
          link
        >
          <v-list-item-title @click="$router.push({ name: menu.route })">{{ menu.text }}</v-list-item-title>
        </v-list-item>
      </v-list-group>

      <!-- Pembaca Button Group -->
      <v-list-group
        :value="false"
        color="white"
        active-class="grey darken-4"
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>Pembaca</v-list-item-title>
          </v-list-item-content>
        </template>

        <v-list-item
          v-for="menu in pembacas"
          :key="menu.text"
          link
        >
          <v-list-item-title @click="$router.push({ name: menu.route })">{{ menu.text }}</v-list-item-title>
        </v-list-item>
      </v-list-group>

      <!-- Admin Button Group -->
      <v-list-group
        :value="false"
        color="white"
        active-class="grey darken-4"
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>Admin</v-list-item-title>
          </v-list-item-content>
        </template>

        <v-list-item
          v-for="menu in admins"
          :key="menu.text"
          link
        >
          <v-list-item-title @click="$router.push({ name: menu.route })">{{ menu.text }}</v-list-item-title>
        </v-list-item>
      </v-list-group>

      <!-- Sign Out Button -->
      <v-list-group
        :value="false"
        color="white"
        active-class="grey darken-4"
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title @click="signout()">Sign Out</v-list-item-title>
          </v-list-item-content>
        </template>
      </v-list-group>
    </v-navigation-drawer>
  </v-card>
</template>

<script>
import { store } from '../../../store/index'
  export default {
    name: 'navbar',
    data: () => ({
        beritas: [
          { text: 'Daftar Berita', route: 'daftar-berita' },
          { text: 'Unggah Berita', route: 'add-berita' },
          { text: 'Kategori', route: 'kategori'}
        ],
        pembacas: [
          { text: 'Daftar Pembaca', route: 'daftar-pembaca' }
        ],
        admins: [
          { text: 'Tambah Admin', route: 'add-admin' },
        ],
    }),
     methods: {
      async signout(){
        const status = await store.getters["admin/isTokenExist"]
        console.log("status token:")
        console.log(status)
       const logoutResult = await store.dispatch("admin/logOut") 
       if(logoutResult instanceof Error){
          this.error.message = logoutResult.cause;
          this.error.isError = true;
       } else {
         const status2 = await store.getters["admin/isTokenExist"]
         console.log("status2 token:")
        console.log(status2)
        this.$router.push("/admin/signin")
       }
      
      }
    }
  }
</script>

<style scoped>
* {
  color: white;
}

v-card {
  justify-content: center;
}

img {
  max-width: 200px;
}
</style>