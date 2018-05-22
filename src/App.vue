<template lang="html">
  <div>
    <tab-bar @filter="onFilter" :colors="colors"></tab-bar>
    <ul>
    <item v-for="item in todoList.filter((x) => filter === 'all' ? x : filter === 'starred' ? x.starred : filter === x.color)"
          :key="item.id" :data="item" :colors="colors" :filter="filter"
          @check="onCheck(item.id)" @color-change="onColorChange(item.id)" @new-color="onNewColor"
          @star-change="onStarChange(item.id)" @remove="onRemove(item.id)"></item>
    </ul>
    <add-form @add-item="addItem"></add-form>
  </div>
</template>

<script>
import Item from './Item.vue'
import TabBar from './TabBar.vue'
import AddForm from './AddForm.vue'

let startArray = [
  {text: 'Agregar elementos con el boton +', color: 'ice',
  checked: false, starred: false, id: 1},
  {text: 'Se pueden filtrar items por color o por la estrella', color: 'jewel',
   checked: true, starred: true, id: 2 },
  {text: 'Para agregar elementos a un color, primero seleccione el color', color: 'tang',
   checked: false, starred: true, id: 3 },
   {text: 'La informacion se almasena en el Storage del navegador', color: 'ice', checked: true, starred: false, id: 4}
]

export default  {
  props: ['stored-id', 'stored-data'],
  components: { AddForm, Item, TabBar },
  data: function(){
    return {
      colors: ['all', 'ice', 'tang', 'jewel', 'rose'],
      newId: this.storedId || 5,
      todoList: this.storedData || startArray,
      filter: 'all',
      recentColor: 'ice'
    }
  },
  watch: {
    todoList: {
      handler: function(){ this.$emit('update-list', this.todoList)},
      deep: true
    }
  },
  methods: {
    onFilter: function(filter){
      this.filter = filter
      if(filter !== 'all' && filter !== 'starred'){
        this.recentColor = filter
      }
    },
    onCheck: function(id){
      let el = this.todoList.find((x) => x.id === id)
      el.checked = !el.checked
    },
    onColorChange: function(id){
      let el = this.todoList.find((x) => x.id === id)
      el.color = this.recentColor
    },
    onNewColor: function(color){
      this.recentColor = color
    },
    onStarChange: function(id){
      let el = this.todoList.find((x) => x.id === id)
      el.starred = !el.starred
    },
    onRemove: function(id){
      let el = this.todoList.find((x) => x.id === id)
      let idx = this.todoList.indexOf(el)
      this.todoList.splice(idx, 1)
    },
    addItem: function(text){
      this.todoList.push({
        text: text,
        color: this.recentColor,
        checked: false,
        starred: false,
        id: this.newId
      })
      this.newId++
      this.$emit('update-id', this.newId)
    }
  }
}
</script>

<style lang="css">
</style>
