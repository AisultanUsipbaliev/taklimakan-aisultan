<template>
  <div id="new-card">
    <div class="form">
      <div class="form_header">Создать карту</div>
      <div class="form_title">
        <div>
          <input type="text" placeholder="Название" v-model="title" @input="fieldHandler">
        </div>
      </div>
      <div class="form_description">
        <div>
          <textarea name="description" id="description" cols="30" rows="10" placeholder="Описание" v-model="description" @input="fieldHandler"></textarea>
        </div>
      </div>
      <div class="form_button-container">
        <div class="button button_back" @click="backToHome()">Отмена</div>
        <div v-if="card" class="button" v-bind:class="[fieldFilled ? 'button_action' : 'button_disable']" @click="editCard()">Сохранить</div>
        <div v-else class="button" v-bind:class="[fieldFilled ? 'button_action' : 'button_disable']" @click="addCard()">Добавить</div>
      </div>
    </div>
  </div>
</template>

<script>
import { uuidv4 } from '../assets/js/utils.js'
export default {
  name: 'NewCard',
  props: [
    'card'
  ],
  data () {
    return {
      id: '',
      title: '',
      description: '',
      fieldFilled: Boolean,
      cards: [[], [], [], []]
    }
  },
  created () {
    this.id = this.card?.id ?? ''
    this.title = this.card?.title ?? ''
    this.description = this.card?.description ?? ''
    this.fieldHandler()

    const cards = localStorage.getItem('cards')
    if (cards) {
      try {
        this.cards = JSON.parse(cards)
      } catch (e) {
        console.log(e)
      }
    }
  },
  methods: {
    addCard () {
      if (this.fieldFilled) {
        const id = uuidv4()

        const c = JSON.parse(JSON.stringify(this.cards[0]))
        c.push({ id: id, title: this.title, description: this.description })
        this.$set(this.cards, 0, c)

        localStorage.setItem('cards', JSON.stringify(this.cards))

        this.backToHome()
      }
    },
    editCard () {
      if (this.fieldFilled) {
        if (this.id) {
          let updated = false
          for (let j = 0; j < this.cards.length; j++) {
            for (let i = 0; i < this.cards[j].length; i++) {
              if (this.cards[j][i].id === this.id) {
                this.cards[j][i].title = this.title
                this.cards[j][i].description = this.description
                updated = true
              }
            }
          }
          if (updated) {
            localStorage.setItem('cards', JSON.stringify(this.cards))
          }
          this.backToHome()
        } else {
          this.addCard()
        }
      }
    },
    fieldHandler () {
      if (this.title.trim().length && this.description.trim().length) {
        this.fieldFilled = true
      } else {
        this.fieldFilled = false
      }
    },
    backToHome () {
      this.$router.push({ path: '/' })
    }
  }
}
</script>

<style lang="scss">
#new-card {
  box-sizing: border-box;
  height: 100vh;
  min-height: 465px;
  background-color: #f5f7fb;
  display: flex;
  align-items: center;
  justify-content: center;
}
.form {
  box-sizing: border-box;
  flex-basis: 572px;
  width: 272px;
  min-width: 272px;
  margin: 0 30px;
  padding: 30px;
  background-color: #fff;
  box-shadow: 0px 0px 5px 0px rgba(0, 0, 0, 0.15);
  border-radius: 10px;
  .form_header {
    width: 100%;
    padding: 0 0 26px 0;
    color: #495168;
    font-size: 26px;
    line-height: 1.2;
    text-align: left;
  }
  .form_title, .form_description {
    div {
      width: 100%;
      background-color: #fff;
      border: 1px solid #e6e6e6;
      margin-bottom: 20px;
      input, textarea {
        width: 100%;
        height: 62px;
        padding: 0 20px;
        line-height: 1.2;
        font-size: 18px;
        color: #1b3815;
        box-sizing: border-box;
        background: transparent;
        outline: none;
        border: none;
        overflow: visible;
      }
    }
  }
  .form_description {
    div {
      textarea {
        min-height: 200px;
        padding: 20px;
        resize: none;
      }
    }
  }
  .form_button-container {
    display: flex;
    justify-content: flex-end;
    color: #495168;
  }
}
</style>
