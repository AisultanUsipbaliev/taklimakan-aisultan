<template>
  <div id="popup">
    <div class="popup-container">
      <div class="popup-background"></div>
      <div class="popup">
        <div class="popup__close" @click="closePopup()">
          <div>&times</div>
        </div>
        <div class="popup__title">{{card.title}}</div>
        <div class="popup__description">{{card.description}}</div>
        <div class="popup__button-container">
          <div class="button button_back" @click="deleteCard()">Удалить</div>
          <div class="button button_action" @click="editCard()">Изменить</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Popup',
  props: ['card'],
  methods: {
    closePopup () {
      this.$emit('closePopup')
    },
    editCard: function (e) {
      this.closePopup()
      this.$router.push({ name: 'NewCard', params: { card: this.card } })
    },
    deleteCard: function (e) {
      this.$emit('delete:data', this.card)
      this.closePopup()
    }
  }
}
</script>

<style lang="scss">
#popup {
  width: 100%;
  height: 100%;
  position: fixed;
  z-index: 3;
  top: 0;
  left: 0;
}
.popup-container {
  position: relative;
  width: 100vw;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  .popup-background {
    background-color: #000;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    opacity: .3;
    z-index: 1;
  }
  .popup {
    background-color: #fff;
    opacity: 1;
    z-index: 2;
    box-sizing: border-box;
    flex-basis: 572px;
    width: 272px;
    min-width: 272px;
    margin: 0 30px;
    padding: 30px;
    background-color: #fff;
    box-shadow: 0px 0px 5px 0px rgba(0, 0, 0, 0.15);
    border-radius: 10px;
    .popup__close {
      display: flex;
      justify-content: flex-end;
      div {
        font-size: 25px;
        cursor: pointer;
      }
    }
    .popup__title {
      width: 100%;
      padding: 0 0 26px 0;
      color: #495168;
      font-size: 22px;
      text-align: left;
    }
    .popup__description {
      width: 100%;
      padding: 0 0 26px 0;
      text-align: left;
      color: #7b838e;
      line-height: 20px;
      font-size: 16px;
    }
    .popup__button-container {
      display: flex;
      justify-content: flex-end;
      color: #495168;
    }
  }
}
</style>
