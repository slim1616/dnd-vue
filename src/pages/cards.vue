<template>
  <div class="card-scene">
    <Container
      orientation="horizontal"
      @drop="onColumnDrop($event)"
      drag-handle-selector=".column-drag-handle"
      @drag-start="dragStart"
      :drop-placeholder="upperDropPlaceholderOptions"
    >
      <Draggable v-for="column in scene.children" :key="column.id">
        <div :class="column.props.className">
          <div class="card-column-header">
            <span class="column-drag-handle">&#x2630;</span>
            {{ column.name }}
          </div>
          <Container
            group-name="col"
            @drop="(e) => onCardDrop(column.id, e)"
            @drag-start="(e) => log('drag start', e)"
            @drag-end="(e) => log('drag end', e)"
            :get-child-payload="getCardPayload(column.id)"
            drag-class="card-ghost"
            drop-class="card-ghost-drop"
            :drop-placeholder="dropPlaceholderOptions"
          >
            <Draggable v-for="card in column.children" :key="card.id">
              <div :class="card.props.className" :style="card.props.style">
                <p v-html="card.data">{{ card.data }}</p>
              </div>
            </Draggable>
          </Container>
        </div>
      </Draggable>
    </Container>
  </div>
</template>

<script>
import { Container, Draggable } from 'vue-smooth-dnd'
import { applyDrag, generateItems } from '../utils/helpers'

const lorem = `Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. 
Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. 
Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.`



export default {
  name: 'Cards',

  components: {Container, Draggable},

  data () {
    return {
      scene :  {
      type: 'container',
      props: {
        orientation: 'horizontal'
      },
      children: [{
        id: `column1`,
        type: 'container',
        name: 'Plan',
        props: {
          orientation: 'vertical',
          className: 'card-container'
        },
        children: []
      },
      {
        id: `Resources`,
        type: 'container',
        name: 'Ressources',
        props: {
          orientation: 'vertical',
          className: 'card-container'
        },
        children: [{
      type: 'draggable',
      id: `1`,
      props: {
        className: 'card',
        style: {backgroundColor: '#b39ddb'}
      },
      data: `<div>
              <span class="close" @click="click()">X</span>
              <b>lorem</b>
              <div style="background:blue; float:left">div1</div>
              <div style="background:azure">div2</div>
              </div>`
    },
    {
      type: 'draggable',
      id: `2`,
      props: {
        className: 'card',
        style: {backgroundColor:'#00BCD4'}
      },
      data: '<b>lorem</b>'
    },
    {
      type: 'draggable',
      id: `3`,
      props: {
        className: 'card',
        style: {backgroundColor: '#03a9f4'}
      },
      data: '<b>lorem</b>'
    }]
      }],
    },
      upperDropPlaceholderOptions: {
        className: 'cards-drop-preview',
        animationDuration: '150',
        showOnTop: true
      },
      dropPlaceholderOptions: {
        className: 'drop-preview',
        animationDuration: '150',
        showOnTop: true
      }
    }
  },

  methods: {
    click(evt){
      console.log(evt)
    },
    onColumnDrop (dropResult) {
      const scene = Object.assign({}, this.scene)
      scene.children = applyDrag(scene.children, dropResult)
      this.scene = scene
    },

    onCardDrop (columnId, dropResult) {
      if (dropResult.removedIndex !== null || dropResult.addedIndex !== null) {
        const scene = Object.assign({}, this.scene)
        const column = scene.children.filter(p => p.id === columnId)[0]
        const columnIndex = scene.children.indexOf(column)

        const newColumn = Object.assign({}, column)
        newColumn.children = applyDrag(newColumn.children, dropResult)
        scene.children.splice(columnIndex, 1, newColumn)

        this.scene = scene
      }
    },

    getCardPayload (columnId) {
      return index => {
        return this.scene.children.filter(p => p.id === columnId)[0].children[index]
      }
    },

    dragStart () {
      console.log('drag started')
    },

    log (...params) {
      console.log(...params)
    }
  }
}
</script>
<style type="text/css">
  

.close{
  cursor: pointer;
  top: 10px;
  position: absolute;
  right: 10px;
}

</style> 