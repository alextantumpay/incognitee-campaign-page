<template>
  <div class="radial-gradient" />
</template>
<style lang="scss">
.radial-gradient {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  opacity: 0.5;
  background: radial-gradient(
    50% 50% at 50% 50%,
    rgba(36, 173, 124, 0.5) 0%,
    rgba(24, 69, 185, 0) 100%
  );
  z-index: -1;
}
</style>
