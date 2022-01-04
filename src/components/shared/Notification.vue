<template lang="pug">
  .container
    .columns
      .column.is-5.is-offset-4
          .notification(:class="{'is-danger' : notificationType === 'danger','is-success' : notificationType === 'success'}")
            slot(name="body")
</template>

<script>
export default {
  props: {
    track: { type: String },
    notificationType: { type: String }
  }
};
</script>

<style lang="scss" scoped>
.notification {
  margin: 10px;
}
</style>
