<template>
  <div>
    <transition name="fade">
      <div v-if="show" class="modal is-active">
        <div class="modal-background" @click="$emit('close')" />
        <div class="modal-content fd-modal-card">
          <div class="card">
            <div class="card-content">
              <p class="title is-4" v-text="directory" />
            </div>
            <footer class="card-footer">
              <a class="card-footer-item has-text-dark" @click="queue_add">
                <span class="icon"
                  ><mdicon name="playlist-plus" size="16"
                /></span>
                <span class="is-size-7" v-text="$t('dialog.directory.add')" />
              </a>
              <a class="card-footer-item has-text-dark" @click="queue_add_next">
                <span class="icon"
                  ><mdicon name="playlist-play" size="16"
                /></span>
                <span
                  class="is-size-7"
                  v-text="$t('dialog.directory.add-next')"
                />
              </a>
              <a class="card-footer-item has-text-dark" @click="play">
                <span class="icon"><mdicon name="play" size="16" /></span>
                <span class="is-size-7" v-text="$t('dialog.directory.play')" />
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
  name: 'ModalDialogDirectory',
  props: ['show', 'directory'],
  emits: ['close'],

  methods: {
    play: function () {
      this.$emit('close')
      webapi.player_play_expression(
        'path starts with "' + this.directory.path + '" order by path asc',
        false
      )
    },

    queue_add: function () {
      this.$emit('close')
      webapi.queue_expression_add(
        'path starts with "' + this.directory.path + '" order by path asc'
      )
    },

    queue_add_next: function () {
      this.$emit('close')
      webapi.queue_expression_add_next(
        'path starts with "' + this.directory.path + '" order by path asc'
      )
    }
  }
}
</script>

<style></style>
