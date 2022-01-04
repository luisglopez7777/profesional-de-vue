<template lang="pug">
  main

    pm-notification(v-show="showNotification", :notificationType="notificationType")
      p( v-show="notificationType === 'danger'",slot="body") No se encontraron resultados
      p( v-show="notificationType === 'success'",slot="body") Se encontraron {{this.tracks.length}} resultados

    pm-loader(v-show="isLoading")
    section.section(v-show="!isLoading")
      nav.nav
        .container
          input.input.is-large(
            type="text",
            placeholder="Buscar canciones",
            v-model="searchQuery",
            @keyup.enter="search"
          )
          a.button.is-info.is-large(@click="search") Buscar
          a.button.is-danger.is-large &times;
      .container
        p
          small {{ searchMessage }}

      .container.results
        .columns.is-multiline
          .column.is-one-quarter(v-for="t in tracks")
            pm-track(
              v-blur="t.preview_url"
              :class="{'is-active': t.id === selectedTrack}",
              :track="t",
              @select="setSelectedTrack")

</template>

<script>
import trackService from "@/services/track";
import PmTrack from "@/components/Track.vue";
import PmNotification from "@/components/shared/Notification.vue";
import PmLoader from "@/components/shared/Loader.vue";

export default {
  name: "app",

  components: { PmTrack, PmLoader, PmNotification },

  data() {
    return {
      searchQuery: "",
      tracks: [],
      isLoading: false,
      selectedTrack: "",
      showNotification: false,
      notificationType: ""
    };
  },

  computed: {
    searchMessage() {
      return `Encontrados: ${this.tracks.length}`;
    }
  },

  watch: {
    showNotification() {
      if (this.showNotification) {
        setTimeout(() => {
          this.showNotification = false;
        }, 3000);
      }
    }
  },

  methods: {
    search() {
      if (!this.searchQuery) {
        return;
      }

      this.isLoading = true;

      trackService.search(this.searchQuery).then(res => {
        this.showNotification = true;
        this.notificationType = res.tracks.total === 0 ? "danger" : "success";
        this.tracks = res.tracks.items;
        this.isLoading = false;
      });
    },

    setSelectedTrack(id) {
      this.selectedTrack = id;
    }
  }
};
</script>

<style lang="scss">
.results {
  margin-top: 50px;
}

.is-active {
  border: 3px solid #23d160;
}
</style>
