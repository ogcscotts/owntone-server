<template>
  <content-with-hero>
    <template #heading-left>
      <h1 class="title is-5" v-text="album.name" />
      <h2 class="subtitle is-6 has-text-link has-text-weight-normal">
        <a class="has-text-link" @click="open_artist" v-text="album.artist" />
      </h2>
      <div class="buttons fd-is-centered-mobile fd-has-margin-top">
        <a class="button is-small is-dark is-rounded" @click="play">
          <span class="icon"><mdicon name="play" size="16" /></span>
          <span v-text="$t('page.audiobooks.album.play')" />
        </a>
        <a
          class="button is-small is-light is-rounded"
          @click="show_album_details_modal = true"
        >
          <span class="icon"><mdicon name="dots-horizontal" size="16" /></span>
        </a>
      </div>
    </template>
    <template #heading-right>
      <p class="image is-square fd-has-shadow fd-has-action">
        <cover-artwork
          :artwork_url="album.artwork_url"
          :artist="album.artist"
          :album="album.name"
          @click="show_album_details_modal = true"
        />
      </p>
    </template>
    <template #content>
      <p
        class="heading is-7 has-text-centered-mobile fd-has-margin-top"
        v-text="
          $t('page.audiobooks.album.track-count', { count: album.track_count })
        "
      />
      <list-tracks :tracks="tracks" :uris="album.uri" />
      <modal-dialog-album
        :show="show_album_details_modal"
        :album="album"
        :media_kind="'audiobook'"
        @close="show_album_details_modal = false"
      />
    </template>
  </content-with-hero>
</template>

<script>
import ContentWithHero from '@/templates/ContentWithHero.vue'
import ListTracks from '@/components/ListTracks.vue'
import ModalDialogAlbum from '@/components/ModalDialogAlbum.vue'
import CoverArtwork from '@/components/CoverArtwork.vue'
import webapi from '@/webapi'

const dataObject = {
  load: function (to) {
    return Promise.all([
      webapi.library_album(to.params.album_id),
      webapi.library_album_tracks(to.params.album_id)
    ])
  },

  set: function (vm, response) {
    vm.album = response[0].data
    vm.tracks = response[1].data.items
  }
}

export default {
  name: 'PageAudiobooksAlbum',
  components: { ContentWithHero, ListTracks, ModalDialogAlbum, CoverArtwork },

  beforeRouteEnter(to, from, next) {
    dataObject.load(to).then((response) => {
      next((vm) => dataObject.set(vm, response))
    })
  },
  beforeRouteUpdate(to, from, next) {
    const vm = this
    dataObject.load(to).then((response) => {
      dataObject.set(vm, response)
      next()
    })
  },

  data() {
    return {
      album: {},
      tracks: [],

      show_album_details_modal: false
    }
  },

  methods: {
    open_artist: function () {
      this.show_details_modal = false
      this.$router.push({ path: '/audiobooks/artists/' + this.album.artist_id })
    },

    play: function () {
      webapi.player_play_uri(this.album.uri, false)
    },

    play_track: function (position) {
      webapi.player_play_uri(this.album.uri, false, position)
    },

    open_dialog: function (track) {
      this.selected_track = track
      this.show_details_modal = true
    }
  }
}
</script>

<style></style>
