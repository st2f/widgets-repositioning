<template>

  <div class="">
    <GridLayout
      v-model:layout="layout"
      :row-height="30"
       :is-bounded="bounded"
      :vertical-compact="false"
      prevent-collision
    >
      <template #item="{ item }">
        <div v-for="x in [1,2,3,4]" :key="x">item {{ item.i }}</div>
        <!-- {{ `${item.i}${item.static ? '- Static' : ''}` }} -->
      </template>
    </GridLayout>
  </div>

  <div class="container">
    <div class="row">
      <template v-for="box in boxes" :key="box.id">
        <Widget :box="box" :class="box.style" />
      </template>
    </div>
  </div>

</template>


<script setup lang="ts">
import { reactive, ref } from 'vue'
import { GridLayout, GridItem } from 'grid-layout-plus'
import Widget from './assets/Widget.vue'

const boxes = [
  {
    id: 1,
    position: 1,
    style: "col-3",
    text: "Lorem ipsum"
  },
  {
    id: 2,
    position: 2,
    style: "col-3",
    text: "Lorem ipsum dolor sit amet consectetur adipisicing elit. Dolorum deserunt voluptatum magnam totam"
  },
  {
    id: 3,
    position: 3,
    style: "col-3",
    text: "Lorem ipsum"
  },
  {
    id: 4,
    position: 4,
    style: "col-6",
    text: "Lorem ipsum"
  },
  {
    id: 5,
    position: 1,
    style: "col-4",
    text: "Lorem ipsum"
  },
  {
    id: 6,
    position: 2,
    style: "col-4",
    text: "Lorem ipsum dolor sit amet consectetur adipisicing elit. Dolorum deserunt voluptatum magnam totam"
  },
  {
    id: 7,
    position: 3,
    style: "col-4",
    text: "Lorem ipsum"
  }

]

// based on a pre-defined dashboard, show (re-position) only the authorized widgets

const responsive = ref(true)
const bounded = ref(true)

const originalWidgets = [
  { x: 0, y: 0, w: 2, h: 2, i: '0' },
  { x: 2, y: 0, w: 2, h: 2, i: '1' },
  { x: 4, y: 0, w: 2, h: 2, i: '2' },
  { x: 6, y: 0, w: 2, h: 2, i: '3' },
  { x: 8, y: 0, w: 2, h: 2, i: '4' },
  { x: 10, y: 0, w: 2, h: 2, i: '5' },
  { x: 0, y: 5, w: 4, h: 2, i: '6' },
  { x: 4, y: 5, w: 2, h: 2, i: '7' },
  { x: 6, y: 5, w: 2, h: 2, i: '8' },
]

const limitedWidgets = [
  { x: 2, y: 0, w: 2, h: 2, i: '1' },
  { x: 4, y: 0, w: 2, h: 2, i: '2' },
  { x: 6, y: 0, w: 2, h: 2, i: '3' },
  { x: 8, y: 0, w: 2, h: 2, i: '4' },
  { x: 10, y: 0, w: 2, h: 2, i: '5' },
  { x: 0, y: 5, w: 4, h: 2, i: '6' },
]

const result = repositionLimitedWidgets(originalWidgets, limitedWidgets)
console.log(result)

// we could take off unauthorized widget id
// const result = removeWidgetAndReposition(originalWidgets, '6')
// console.log(result)

const layout = reactive(result)

function repositionLimitedWidgets(originalWidgets, limitedWidgets) {
  // create a map of original widgets by their IDs for quick lookups
  const originalWidgetMap = originalWidgets.reduce((acc, widget) => {
    acc[widget.i] = widget
    return acc
  }, {})

  // reposition the limited widgets
  let x = 0
  let y = 0

  const updatedLimitedWidgets = limitedWidgets.map((widget) => {
    // check if the widget's width causes it to overflow horizontally
    if (x + widget.w > 12) {
      // if it does, move to the next row
      x = 0
      y += 2 // Increase y (height is 2, so we increment by 2)
    }

    // find the original widget by ID (although limitedWidget already contains the widget structure, for clarity we keep the original's structure)
    const originalWidget = originalWidgetMap[widget.i]

    // set the new position for the widget
    const updatedWidget = {
      ...originalWidget, // keep original properties
      x: x,
      y: y,
    }

    // update x for the next widget
    x += widget.w

    return updatedWidget
  })

  return updatedLimitedWidgets
}

function removeWidgetAndReposition(matrix, id) {
  // remove the widget with the given id
  matrix = matrix.filter((widget) => widget.i !== id)

  // reposition the remaining widgets
  let x = 0
  let y = 0

  // create a new matrix for repositioned widgets
  const updatedMatrix = matrix.map((widget) => {
    // check if the widget's width causes it to overflow horizontally
    if (x + widget.w > 12) {
      // ff it does, move to the next row
      x = 0
      y += 2 // increase y (height is 2, so we increment by 2)
    }

    // set the new position for the widget
    const newWidget = {
      ...widget,
      x: x,
      y: y,
    }

    // update x for the next widget
    x += widget.w

    return newWidget
  })

  return updatedMatrix
}
</script>


<style lang="scss" scoped>
//@import './assets/base.scss';

.vgl-layout {
  background-color: #eee;

}

:deep(.vgl-item:not(.vgl-item--placeholder)) {
  background-color: #ccc;
  border: 1px solid black;
}

:deep(.vgl-item--resizing) {
  opacity: 90%;
}

:deep(.vgl-item--static) {
  background-color: #cce;
}

</style>
