<template>
  <v-container>
    <virtual-list 
      ref="virtual-scroller" 
      class="list-infinite scroll-touch item" 
      :page-mode="true" 
      :data-key="'id_berita'" 
      :data-sources="mostLikedBerita" 
      :data-component="itemComponent" 
      v-on:tobottom="onScrollToBottom" 
      :keeps="20">
      <div slot="footer">
        <div class="loader itemStillExist" v-if="popularLoading">
          <v-progress-circular indeterminate color="#E52B38"></v-progress-circular>
        </div>
        <div class="text-center" v-if="isEndOfList">
          <h3>No more item</h3>
         </div>
      </div>
    </virtual-list>
  </v-container>
</template>

<script>
//const PRIVIEW_MAX_WORDS = 70
import beritaApi from "../../../api/berita/berita";
import { BASE_URL } from "../../../api/const";
import MostLikesItem from "./SearchResultItem"
const NEWS_PERCALL = 5;
export default {
  created() {
    this.retrieveMostLikedBerita();
  },
  data() {
    return {
      itemComponent : MostLikesItem,
      mostLikedBerita: [],
      isError: false,
      errorMessage: "",
      popularLoading: false,
      pageNum: 1,
      isEndOfList: false,
    };
  },
  watch: {
    '$route' : function () {
      this.mostLikedBerita = [];
      this.pageNum = 1
      this.isEndOfList = false
      this.retrieveMostLikedBerita();
    },
  },

  methods: {
    onScrollToBottom() {
      if(!this.popularLoading && !this.isEndOfList) {
        this.retrieveMostLikedBerita();
      }
    },
    async retrieveMostLikedBerita() {
      this.popularLoading = true;
      //benerin perpage sama page nya, parameternya teh (perpage, key, page)
      const result = await beritaApi.popularBerita(NEWS_PERCALL, this.$route.query.q, this.pageNum);
      this.pageNum++;
      this.popularLoading = false;
      if (result instanceof Error) {
        this.isError = true;
        this.errorMessage = "Gagal mendapatkan berita karena " + result.cause;
        return;
      }
      if(result.data.length <= 0) {
        if(this.isEndOfList) {
          return;
        }
        this.isEndOfList = true;
      }
      result.data.forEach((element) => {
        element.url_gambar = BASE_URL + "/" + element.url_gambar;
        this.mostLikedBerita.push(element);
      });
      console.log("Search by Most Likes");
      console.log(this.mostLikedBerita);
    },
  }
};
</script>

<style scoped>
.loader {
  position: relative;
  left: 45%;
}

.list-infinite::-webkit-scrollbar {
  display: none;
}

.list-infinite {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  padding: 0 10rem;
  position: relative;
}

.item {
  cursor:pointer;
}

@media screen and (max-width: 960px) {
  .list-infinite {
    padding: 0 0;
  }
}
</style>