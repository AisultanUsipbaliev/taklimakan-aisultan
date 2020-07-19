<template>
  <div id="main">
    <div class="header">
      <div class="header__logo-container">Taklimakan Task</div>
      <div class="header__button-container">
        <div class="header__toggle-container">
          <label for="toggle">Перетаскивание</label>
          <div class="header__toggle">
            <input id="toggle" type="checkbox" class="toggle" v-model="enableDrag" checked>
          </div>
        </div>
        <div class="add-btn">
          <router-link to="/new_card">
            <img src="../assets/img/add.svg" alt="add-btn">
          </router-link>
        </div>
      </div>
    </div>
    <drag-and-drop class="dad" v-bind:cards="cards" v-bind:enableDrag="enableDrag" v-on:update:data="updateCards"></drag-and-drop>
  </div>
</template>

<script>
import DragAndDrop from '../components/DragAndDrop.vue'
export default {
  name: 'Main',
  components: {
    DragAndDrop
  },
  data () {
    return {
      enableDrag: true,
      cards: [
        [{
          id: 'bc55071fffb14553ac5eb3a5e744a6f3',
          title: 'First',
          description: 'Lorem ipsum dolor sit amet, consectetur adipisicing elit. Maxime placeat tempore, laborum obcaecati dicta corporis aliquam laboriosam similique porro odio ipsa libero ducimus quibusdam fuga ullam numquam, animi ex exercitationem.'
        }],
        [{
          id: '1180bd9bc13a4d8f9138407e9f6f5868',
          title: 'Second',
          description: 'Lorem ipsum dolor sit amet, consectetur adipisicing elit. Excepturi accusantium voluptatibus quos minus, autem architecto illum ea, hic velit, tempore, quidem aliquam rem porro maxime quae magni exercitationem tenetur fugit!'
        }],
        [],
        []
      ],
      dragstartElementsIndex: []
    }
  },
  created () {
    const cards = localStorage.getItem('cards')
    if (cards) {
      try {
        this.cards = JSON.parse(cards)
      } catch (e) {
        console.log(e)
      }
    } else {
      localStorage.setItem('cards', JSON.stringify(this.cards))
    }
  },
  methods: {
    updateCards (data) {
      this.cards = data
    }
  }
}

</script>

<style lang="scss">
#main {
  width: 100%;
  height: 100%;
}
.header {
  display: flex;
  align-items: center;
  height: 70px;
  padding: 0 30px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1;
  background-color: #fff;
  box-shadow: 0px 2px 5px 0px rgba(0, 0, 0, 0.05);
  .header__logo-container {
    width: 200px;
    color: #495168;
    font-size: 20px;
    @media only screen and (max-width: 460px) {
      & {
        display: none;
      }
    }
  }
  .header__button-container {
    flex: 1;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    @media only screen and (max-width: 460px) {
      & {
        justify-content: center;
      }
    }
    .header__toggle-container {
      display: flex;
      align-items: center;
      margin-right: 15px;
      label {
        margin-right: 15px;
        color: #495168;
        font-size: 18px;
        cursor: pointer;
      }
      .header__toggle {
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
}
.dad {
  margin-top: 70px;
  height: calc(100vh - 70px);
  overflow: overlay;
  background-color: #f5f7fb;
}
input[type="checkbox"] {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  -webkit-tap-highlight-color: transparent;
  cursor: pointer;
  &:focus {
    outline: 0;
  }
}
.toggle {
  height: 32px;
  width: 52px;
  border-radius: 16px;
  display: inline-block;
  position: relative;
  margin: 0;
  border: 2px solid #495168;
  background: linear-gradient(180deg, #fff 0%, #fff 100%);
  transition: all .2s ease;
  &:after {
    content: '';
    position: absolute;
    top: 2px;
    left: 2px;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    background: #dadada;
    box-shadow: 0 1px 2px rgba(44,44,44,.2);
    transition: all .2s cubic-bezier(.5,.1,.75,1.35);
  }
  &:checked {
    &:after {
      background: #0095ea;
      transform: translatex(20px);
    }
  }
}
.add-btn {
  width: 32px;
  height: 32px;
  padding: 0;
  margin: 5px;
  border-radius: 50%;
  background-color: #fff;
  &:hover {
    width: 28px;
    height: 28px;
    padding: 2px;
    margin: 5px;
    background-color: #0095ea;
  }
  img {
    width: 100%;
    height: 100%;
  }
}
</style>
