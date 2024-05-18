<template>
  <div class="bg-style">
    <div class="d-flex justify-content-center pt-4">
      <input type="text" class="newTodo border-0 rounded-3" :placeholder="placeholder"
      @keydown.enter="addTodo" v-model="newTodo">
      <button type="button" class="btn btnPlus" @click="addTodo">
        <img :src="btnImg" alt="">
      </button>
    </div>
    <div class="mt-4 bg-white lists rounded-3">
      <div class="d-flex justify-content-around">
        <a class="text-decoration-none d-block w-100 text-center py-3 border-3 border-dark border-bottom" href="#">
          <span class="text-dark fw-bold">全部</span>
        </a>
        <a class="text-decoration-none d-block w-100 text-center py-3 border-3  border-bottom" href="#">
          <span class="text-dark fw-bold">待完成</span>
        </a>
        <a class="text-decoration-none d-block w-100 text-center py-3 border-3  border-bottom" href="#">
          <span class="text-dark fw-bold">已完成</span>
        </a>
      </div>
      <div class="px-3">
        <ul class="p-3 m-0">
          <li v-for="list in lists" :key="list.id" @dblclick="list.openEdit = !list.openEdit"
          class="py-2 border-2 border-bottom d-flex justify-content-between">
            <div v-if="list.openEdit">
              <input type="checkbox" 
              :name="list.id" :id="list.id" v-model="list.isDone">
              <label :for="list.id">
                <span class="ms-3" :class="{ 'text-decoration-line-through': list.isDone}">{{ list.text }}</span>
              </label>
            </div>
            <div v-else>
              AAA
            </div>
            <button type="button" class="btn p-0 px-2 removeBtn" @click="removeTodo(list.id)">Ｘ</button>
          </li>
        </ul>
      </div>
      <div class="d-flex justify-content-between px-3">
        <div class="p-3">
          <span>{{ lists.length }} 個待完成項目</span>
        </div>
        <div class="p-3">
          <a href="#" class="text-decoration-none text-secondary">清除已完成項目</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watchEffect } from 'vue';
const placeholder = ref('新增待辦事項')
const btnImg = ref('src/assets/plus.png')

const localData = JSON.parse(localStorage.getItem('todos') || '[]')

const newTodo = ref('')
const lists = ref(localData)

// 新增
const addTodo = () => {
  if(newTodo.value) {
    lists.value.push({
      id: Date.now(),
      text: newTodo.value,
      isDone: false,
      openEdit: true
    })
    newTodo.value = ''
  }
}

// 刪除
const removeTodo = (listId) => {
  const id = lists.value.findIndex((item) => {
    return item.id === listId
  })
  lists.value.splice(id, 1)
}

// 編輯
// const editTodo = () => {
  
// }

// 自動儲存
watchEffect(() => {
  localStorage.setItem('todos', JSON.stringify(lists.value))
})

</script>

<style lang="scss">
ul li {
  list-style-type: none;
}

.bg-style {
  background-image: linear-gradient(175deg, #FFD370 50%, #fff 40%);
  width: 100%;
  height: 100vh;
}

.lists {
  width: 500px;
  margin: 0 auto;
}
.newTodo{
  width: 500px;
  padding: 12px 16px;
}

.btnPlus{
  margin-left: -60px;
}

.removeBtn {
  margin-right: -30px;
}

</style>