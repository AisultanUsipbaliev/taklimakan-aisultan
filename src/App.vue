<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style lang="scss">
@font-face {
  font-family: 'Sora';
  src: url('./assets/font/Sora-VariableFont_wght.ttf');
}
body {
  font-family: 'Sora', sans-serif;
}
.button {
  border: 1px solid #fff;
  padding: 10px;
  margin-left: 20px;
  border-radius: 10px;
  color: #495168;
  font-size: 16px;
  cursor: pointer;
  &:hover {
    border: 1px solid #000;
  }
}
.button_back {
  background-color: #fff;
  border: 1px solid #cccccc;
}
.button_action {
  color: #fff;
  background-color: #0095ea;
}
.button_disable {
  border: 1px solid #999999;
  background-color: #cccccc;
  color: #666666;
  cursor: not-allowed;
  &:hover {
    border: 1px solid #999999;
  }
}
</style>
