<template>
  <div>
    <transition name="fade">
      <div v-if="show" class="modal is-active">
        <div class="modal-background" @click="$emit('close')" />
        <div class="modal-content fd-modal-card">
          <div class="card">
            <div class="card-content">
              <p class="title is-4">
                <a
                  class="has-text-link"
                  @click="open_playlist"
                  v-text="playlist.name"
                />
              </p>
              <div class="content is-small">
                <p>
                  <span class="heading">Path</span>
                  <span class="title is-6" v-text="playlist.path" />
                </p>
                <p>
                  <span class="heading" v-text="$t('dialog.playlist.type')" />
                  <span class="title is-6" v-text="playlist.type" />
                </p>
                <p v-if="!playlist.folder">
                  <span
                    class="heading"
                    v-text="$t('dialog.playlist.track-count')"
                  />
                  <span class="title is-6" v-text="playlist.item_count" />
                </p>
              </div>
            </div>
            <footer v-if="!playlist.folder" class="card-footer">
              <a class="card-footer-item has-text-dark" @click="queue_add">
                <span class="icon"
                  ><mdicon name="playlist-plus" size="16"
                /></span>
                <span class="is-size-7" v-text="$t('dialog.playlist.add')" />
              </a>
              <a class="card-footer-item has-text-dark" @click="queue_add_next">
                <span class="icon"
                  ><mdicon name="playlist-play" size="16"
                /></span>
                <span
                  class="is-size-7"
                  v-text="$t('dialog.playlist.add-next')"
                />
              </a>
              <a class="card-footer-item has-text-dark" @click="play">
                <span class="icon"><mdicon name="play" size="16" /></span>
                <span class="is-size-7" v-text="$t('dialog.playlist.play')" />
              </a>
            </footer>
          </div>
        </div>
        <button
          class="modal-close is-large"
          aria-label="close"
          @click="$emit('close')"
        />
      </div>
    </transition>
  </div>
</template>

<script>
import webapi from '@/webapi'

export default {
  name: 'ModalDialogPlaylist',
  props: ['show', 'playlist', 'uris'],
  emits: ['close'],

  methods: {
    play: function () {
      this.$emit('close')
      webapi.player_play_uri(this.uris ? this.uris : this.playlist.uri, false)
    },

    queue_add: function () {
      this.$emit('close')
      webapi.queue_add(this.uris ? this.uris : this.playlist.uri)
    },

    queue_add_next: function () {
      this.$emit('close')
      webapi.queue_add_next(this.uris ? this.uris : this.playlist.uri)
    },

    open_playlist: function () {
      this.$emit('close')
      this.$router.push({ path: '/playlists/' + this.playlist.id + '/tracks' })
    }
  }
}
</script>

<style></style>
