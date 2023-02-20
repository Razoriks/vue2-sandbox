<template>
  <button class="btn-grid" @click="$emit('click')">
    <slot />
  </button>
</template>

<style lang="scss">
.btn-grid {
  background-color: #ffffff;
  border: 2px solid #42b983;
  color: #42b983;
  cursor: pointer;

  &:first-child {
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
    border-right: 1px;
  }

  &:last-child {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
    border-left: 1px;
  }

  &_active {
    background-color: #42b983;
    color: #ffffff;
  }
}
</style>
