<template>
  <div>
    <popup v-bind:card="card" v-if="showPopup" v-on:closePopup="closePopup" v-on:delete:data="deleteCard"></popup>
    <div v-show="enableDrag" class="dad-container">
      <div class="dad-container__cloumn" v-for="(cardIndex, index) in cards" :key="index" >
        <div class="dad-container__row" :draggable="enableDrag" v-for="card in cardIndex" :key="card.id" v-on:dragstart="dragstart($event)" v-on:dragend="dragend($event)">
          <div class="card" @click="showData(card)">
            <div class="card__block">
              <div class="card__title">{{card.title}}</div>
              <div class="card__description">{{card.description}}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-show="!enableDrag" class="unselectable-container">
      <div class="unselectable-container__cloumn" v-for="(cardIndex, index) in cards" :key="index">
        <div class="unselectable-container__row" v-for="card in cardIndex" :key="card.id">
          <div class="card" @click="showData(card)">
            <div class="card__block">
              <div class="card__title">{{card.title}}</div>
              <div class="card__description">{{card.description}}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Popup from '../components/Popup.vue'
export default {
  name: 'DragAndDrop',
  props: ['cards', 'enableDrag'],
  components: {
    Popup
  },
  data () {
    return {
      card: {},
      showPopup: false,
      dragstartElementsIndex: []
    }
  },
  watch: {
    cards: {
      deep: true,
      handler (cards) {
        localStorage.setItem('cards', JSON.stringify(cards))
        this.$emit('update:data', cards)
      }
    }
  },
  mounted () {
    this.setupDragoverListener()
  },
  methods: {
    // получаем начальную позицию перетаскиваемого елемента
    dragstart: function (e) {
      const t = e.target

      const endColumnIndex = Array.from(t.parentNode.parentNode.children).indexOf(t.parentNode)
      const endRowIndex = Array.from(t.parentNode.children).indexOf(t)

      this.dragstartElementsIndex = [endColumnIndex, endRowIndex]

      setTimeout(function () {
        e.target.classList.add('dragging')
      }, 0)
    },
    // получаем конечную позицию перетаскиваемого елемента и изменяем переменную cards
    dragend: function (e) {
      const t = e.target

      const endColumnIndex = Array.from(t.parentNode.parentNode.children).indexOf(t.parentNode)
      const endRowIndex = Array.from(t.parentNode.children).indexOf(t)

      const startColumnIndex = this.dragstartElementsIndex[0]
      const startRowIndex = this.dragstartElementsIndex[1]

      const payload = JSON.parse(JSON.stringify(this.cards[startColumnIndex][startRowIndex]))

      if (startColumnIndex === endColumnIndex) {
        const section = JSON.parse(JSON.stringify(this.cards[startColumnIndex]))
        section.splice(startRowIndex, 1)
        section.splice(endRowIndex, 0, payload)

        this.$set(this.cards, startColumnIndex, section)
      } else {
        const section1 = JSON.parse(JSON.stringify(this.cards[startColumnIndex]))
        section1.splice(startRowIndex, 1)
        const section2 = JSON.parse(JSON.stringify(this.cards[endColumnIndex]))
        section2.splice(endRowIndex, 0, payload)

        this.$set(this.cards, startColumnIndex, section1)
        this.$set(this.cards, endColumnIndex, section2)
      }

      setTimeout(function () {
        e.target.classList.remove('dragging')
        this.dragstartElementsIndex = []
      }, 0)
    },
    setupDragoverListener () {
      const containers = document.querySelectorAll('.dad-container__cloumn')

      // перемещаем перетаскиваемый элемент
      containers.forEach(container => {
        container.addEventListener('dragover', e => {
          e.preventDefault()
          const afterElement = getDragAfterElement(container, e.clientY)
          const draggable = document.querySelector('.dragging')

          if (afterElement == null) {
            container.appendChild(draggable)
          } else {
            container.insertBefore(draggable, afterElement)
          }
        })
      })

      // возвращаем елемент до которого должен стоять перетаскиваемый элемент
      // или undefined если нет елементов после перетаскиваемого элемента
      function getDragAfterElement (container, y) {
        const draggableElements = [...container.querySelectorAll('.dad-container__row:not(.dragging)')]

        return draggableElements.reduce((closest, child) => {
          const box = child.getBoundingClientRect()
          const offset = y - box.top - box.height / 2

          if (offset < 0 && offset > closest.offset) {
            return { offset: offset, element: child }
          } else {
            return closest
          }
        }, { offset: Number.NEGATIVE_INFINITY }).element
      }
    },
    showData (card) {
      this.showPopup = true
      this.card = card
    },
    closePopup () {
      this.showPopup = false
    },
    deleteCard (card) {
      let updated = false
      for (let j = 0; j < this.cards.length; j++) {
        for (let i = 0; i < this.cards[j].length; i++) {
          if (this.cards[j][i].id === card.id) {
            this.cards[j].splice(i, 1)
            updated = true
          }
        }
      }
      if (updated) {
        localStorage.setItem('cards', JSON.stringify(this.cards))
        this.$emit('update:data', this.cards)
      }
    }
  }
}
</script>

<style lang="scss">
%container {
  display: flex;
  width: 100%;
  height: 100%;
}
%column {
  flex: 1;
  flex-basis: 272px;
  width: 272px;
  min-width: 272px;
  padding: 15px;
}
%row {
  margin: 15px;
  background-color: #fff;
  border: 1px solid #eceef0;
  border-radius: 10px;
  cursor: pointer;
}
.dad-container {
  @extend %container;
  .dad-container__cloumn {
    @extend %column;
    .dad-container__row {
      @extend %row;
      .card {
        border-left: 5px solid #0095ea;
      }
    }
  }
}
.unselectable-container {
  @extend %container;
  .unselectable-container__cloumn {
    @extend %column;
    user-drag: none;
    user-select: none;
    .unselectable-container__row {
      @extend %row;
      user-drag: none;
      user-select: none;
      .card {
        border-left: 5px solid #dadada;
      }
    }
  }
}
.dragging {
  opacity: 1;
  background-color: #f3f4f6!important;
  border: 1px dashed #d8dade!important;
  .card {
    visibility: hidden;
  }
}
.card {
  padding: 15px;
  margin: 10px;
  border-left: 5px solid transparent;
  .card__block {
    height: 90px;
    .card__title {
      display:inline-block;
      width: 100%;
      padding: 0 0 7.5px 0;
      color: #495168;
      font-size: 18px;
      font-weight: 600;
      overflow:hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    .card__description {
      display: -webkit-box;
      -webkit-line-clamp: 3;
      -webkit-box-orient: vertical;
      width: 100%;
      overflow: hidden;
      color: #7b838e;
      line-height: 20px;
      font-size: 16px;
    }
  }
}
</style>
