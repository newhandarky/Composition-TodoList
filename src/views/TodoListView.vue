<template>
  <div class="bg-style px-3">
    <div class="d-flex justify-content-center">
      <div class="add-todo pt-4">
        <div class="position-relative">
          <input type="text" class="newTodo border-0 rounded-3" :placeholder="placeholder"
          @keydown.enter="addTodo" v-model="newTodo">
          <button type="button" class="btn btnPlus position-absolute" @click="addTodo">
            <img :src="btnImg" alt="">
          </button>
        </div>
      </div>
    </div>
    <div class="mt-4 bg-white lists rounded-3 shadow py-3 mb-5 bg-body rounded">
      <div class="d-flex justify-content-around">
        <a class="text-decoration-none d-block w-100 text-center pb-2 border-3 border-bottom"
        @click="selected = 'all'" :class="{'border-dark' : selected === 'all'}" href="#">
          <span class="text-secondary " :class="{'text-dark fw-bold' : selected === 'all'}">全部</span>
        </a>
        <a class="text-decoration-none d-block w-100 text-center pb-2 border-3 border-bottom"
        @click="selected = 'notCompleted'" :class="{'border-dark' : selected === 'notCompleted'}"  href="#">
          <span class="text-secondary " :class="{'text-dark fw-bold' : selected === 'notCompleted'}">待完成</span>
        </a>
        <a class="text-decoration-none d-block w-100 text-center pb-2 border-3 border-bottom"
        @click="selected = 'done'" :class="{'border-dark' : selected === 'done'}" href="#">
          <span class="text-secondary " :class="{'text-dark fw-bold' : selected === 'done'}">已完成</span>
        </a>
      </div>
      <div class="px-3">
        <ul class="p-3 m-0">
          <li v-for="list in checkList" :key="list.id" @dblclick="openEdit(list)"
          class="py-2 border-2 border-bottom d-flex justify-content-between">
            <div v-if="list.openEdit">
              <input type="checkbox" 
              :name="list.id" :id="list.id" v-model="list.isDone">
              <label :for="list.id">
                <span class="ms-3" :class="{ 'text-decoration-line-through': list.isDone}">{{ list.text }}</span>
              </label>
            </div>
            <div v-else>
              <input type="text" ref="inputDom" v-model.trim="list.text" @keydown.enter="updateTodo(list)">
            </div>
            <button type="button" class="btn p-0 px-2 removeBtn" @click="removeTodo(list.id)">Ｘ</button>
          </li>
        </ul>
      </div>
      <div class="d-flex justify-content-between px-3">
        <div class="p-3">
          <span>{{ notCompleted }} 個待完成項目</span>
        </div>
        <div class="p-3">
          <a href="#" class="text-decoration-none text-secondary" @click="removeAll">清除已完成項目</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watchEffect } from 'vue';

const placeholder = ref('新增待辦事項')
const btnImg = ref('src/assets/plus.png')

const localData = JSON.parse(localStorage.getItem('todos') || '[]')

const inputDom = ref(null);
const newTodo = ref('')
const lists = ref(localData)
const selected = ref('all')
const notCompleted = ref(0)

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
const removeAll = () => {
  lists.value = []
}

// 編輯
const openEdit = (item) => {  
  item.openEdit = false
}
const updateTodo = (item) => {
  newTodo.value = item.text
  if(newTodo.value) {
    item.openEdit = true
    newTodo.value = ''
  } else {
    alert('請勿空白')
  }
}

// 計算未完成todo數量
const countTodo = () => {
  let num = 0
  lists.value.forEach((item) => {
    if(item.isDone === false) {
      num++
    }
  })
  return num
}

// 切換頁籤
const checkList = computed(() => {
  if (selected.value === 'all') {
    return lists.value;
  } else {
    return selected.value === 'done'
      ? lists.value.filter(item => item.isDone === true)
      : lists.value.filter(item => item.isDone === false);
  }
});

// 自動儲存
watchEffect(() => {
  localStorage.setItem('todos', JSON.stringify(lists.value))

  notCompleted.value = countTodo()
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
  max-width: 500px;
  margin: 0 auto;
}

.add-todo{
  width: 500px;
}
.newTodo{
  width: 500px;
  padding: 12px 16px;
}

.btnPlus{
  right: -8px;
  bottom: -2px;
}

.removeBtn {
  margin-right: -30px;
}

@media (max-width: 576px){
  .newTodo {
    width: 100%;
  }
}

</style>