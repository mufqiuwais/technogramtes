<template>
  <v-container>
    <v-row class="pa-xs-3 pa-sm-3 px-md-10 px-xl-10 px-lg-10">
      <v-col cols="12" :class="isMobile? 'mt-n3 mb-n5' : ''">
        <h1 :class="isMobile? 'title-font ml-n2': 'title-font save-page-title'">Saved Articles</h1>

      </v-col>
     <div :class="isMobile? 'middle-border-mobile' : 'middle-border' "></div>
      <v-col cols="12">
        <v-row :class="isMobile ? 'pa-0' : 'justify-center'">
          <v-col
            :cols="isMobile ? '12' : '10'"
            class="mt-n2"
            v-for="berita in savedBerita"
            :key="berita.id_berita"
            @click="onBeritaSelected(berita.id_berita)"
          >
            <left-image-preview-berita
              class="item"
              v-if="!isMobile"
              :berita="berita"
            ></left-image-preview-berita>
            <right-image-preview-berita
              class="item"
              v-else
              :berita="berita"
            ></right-image-preview-berita>
          </v-col>
        </v-row>
      </v-col>
      <v-col  cols="12">
        <v-row :class="isMobile? 'pa-0' : 'justify-center'">
        <virtual-list ref="virtual-scroller" class="list-infinite scroll-touch" :page-mode="true" :data-key="'id'" :data-sources="savedBerita" v-on:tobottom="onScrollToBottom" :keeps="20">
          <div slot="footer">
            <div class="text-center progress-bar" v-if="beritaLoading">
              <v-progress-circular indeterminate color="#E52B38"></v-progress-circular>
            </div>
            <div class="no-item justify-center text-center" v-if="isEndOfList">
              <h3>No more item</h3>
            </div>
          </div>
        </virtual-list>
        </v-row>
      </v-col>
     </v-row>
  </v-container>
</template>

<script>
import LeftImagePreviewBerita from "../berita/LeftImageBeritaPreview.vue";
import RightImagePreviewBerita from "../berita/RightImageBeritaPreview.vue";

import beritaApi from "../../../api/berita/berita";
import { BASE_URL } from "../../../api/const";
import { store } from '../../../store/index';
const NEWS_PERCALL = 5;

export default {
  created() {
    if (!store.getters["user/isTokenExist"]) {
      this.$router.push("/");
    } else {
      this.retrieveSavedBerita();
    }
  },
  components: {
    LeftImagePreviewBerita,
    RightImagePreviewBerita,
  },
  data() {
    return {
      savedBerita: [],
      isError: false,
      errorMessage: "",
      beritaLoading: false,
      pageNum: 1,
      isEndOfList: false,
      totalPages: 0
    };
  },

  methods: {
    onScrollToBottom() {
      console.log(this.isEndOfList);
      if (!this.beritaLoading && !this.isEndOfList) {
        this.retrieveSavedBerita();
    }
    },
    
    async retrieveSavedBerita() {
      this.beritaLoading = true;
      const result = await beritaApi.savedBeritaList(NEWS_PERCALL, '', this.pageNum, store.getters['user/getToken']);
      this.pageNum++;
      this.beritaLoading = false;

      if (result instanceof Error) {
        return;
      }
       if (result.data.length <= 0 || !result.data.nextPage) {
        if (this.isEndOfList) {
          return;
        }
        this.isEndOfList = true;
        console.log(this.isEndOfList);
      }
      
      result.data.sliceOfSaved.forEach((element) => {
        element.url_gambar = BASE_URL + "/" + element.url_gambar;
        element.id = new Date().toString();
        this.savedBerita.push(element);
      });
     },
  
     onBeritaSelected(id) {
      this.$router
        .push({
          name: "read-berita",
          params: { id: `${id}` },
        })
        .catch((err) => {
          err;
        });
    },
  },
  computed: {
    isMobile() {
      if (this.$vuetify.breakpoint.sm || this.$vuetify.breakpoint.xs) {
        return true;
      }
      return false;
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,600;0,900;1,400&display=swap");
.title-font {
  font-family: "Work sans", serif;
}
.save-page-title {
  margin-left: 18%;
}
.middle-border {
  background: rgb(0, 0, 0, 0.6);
  width: 63%;
  height: 2px;
  margin-left: 18%;
}
.middle-border-mobile {
  background: rgb(0, 0, 0, 0.6);
  width: 100%;
  height: 2px;
}

.item {
  cursor: pointer;
}

.top-right-container {
  position: relative;
}

.progress-bar {
  position: relative;
  top: 45%;
}

.no-item {
  border-top: 1px solid rgb(0, 0, 0, 0.6);
  color:  rgb(0, 0, 0, 0.6);
}

.list-infinite::-webkit-scrollbar {
  display: none;
}

.list-infinite {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  position: relative;
}

@media screen and (max-width: 960px) {
  .list-infinite {
    padding: 0 0;
  }
}
</style>