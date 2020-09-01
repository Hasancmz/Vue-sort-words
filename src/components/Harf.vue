<template>
  <div class="harf shadow mr-3">
    <span>{{ deger }}</span>
  </div>
</template>

<script>
export default {
  props: {
    deger: {
      type: String,
    },
  },
};
</script>

<style scoped lang="scss">
.harf {
  width: 80px;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 30pt;
}
@media screen and (max-width: 500px) {
  .harf {
    width: 30px;
    height: 30px;
    font-size: 16pt;
  }
  .mr-3 {
    margin-right: 5px !important;
  }
}
</style>
