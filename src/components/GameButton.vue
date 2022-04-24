<template>
    <button :class="'game-button click-cursor ' + this.class">
      <div class="title">{{ title }}</div>
      <img src="../assets/images/borders/bubble-arrow.png" alt="">
    </button>
</template>

<script>
export default {
    props: [
      'title',
      'class'
    ],
}
</script>

<style scoped>
.game-button {
  display: block;
  position: absolute;
  background-image: url("../assets/images/borders/text-bubble-left.png"),
    url("../assets/images/borders/text-bubble-right.png");
  background-position: left top, right top;
  background-repeat: repeat-y;
  background-color: rgb(255, 224, 183);
  image-rendering: pixelated;
  animation: buttonAnimation 2s infinite;
  background-size: 2px 2px;
}

.game-button::before,
.game-button::after {
  content: "";
  display: block;
  position: absolute;
}

.game-button::before {
  top: -2.7398px;
  left: 4.7398px;
  width: calc(100% - 9.47959px);
  height: calc(100% + 5.77959px);
  background-image: url("../assets/images/borders/text-bubble-top.png"),
    url("../assets/images/borders/text-bubble-btm.png");
  background-position: center top, center bottom;
  background-repeat: repeat-x;
  background-color: rgb(255, 224, 183);
  background-size: 5px 2px;
}

.game-button::after {
  top: -2.7398px;
  left: 0px;
  width: 100%;
  height: calc(100% + 6.07959px);
  background-image: url("../assets/images/borders/text-bubble-topLeft.png"),
    url("../assets/images/borders/text-bubble-topRight.png"),
    url("../assets/images/borders/text-bubble-btmLeft.png"),
    url("../assets/images/borders/text-bubble-btmRight.png");
  background-position: left top, right top, left bottom, right bottom;
  background-repeat: no-repeat;
  background-size: 5px 5px;
}

.game-button img {
  position: absolute;
  width: 5px;
  height: 4px;
  left: 50%;
  bottom: -4.4px;
  transform: translateX(-50%);
}

.title {
  font-size: 3.5px;
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  position: relative;
  text-align: center;
  color: rgb(0, 0, 0);
  padding: 0px 10.0344px;
  font-family: Lora, serif;
  font-weight: bold;
  white-space: nowrap;
  z-index: 1;
}

@keyframes buttonAnimation {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(3px);
  }
  100% {
    transform: translateY(0);
  }
}

</style>