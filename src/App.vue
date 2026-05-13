<template>
  <div class="container">
    <h1 class="title">我的待办</h1>

    <div class="input-section">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        type="text"
        placeholder="添加新任务..."
        class="input"
      />
      <button @click="addTodo" class="add-btn">添加</button>
    </div>

    <div class="todo-list">
      <TransitionGroup name="todo">
        <div
          v-for="todo in todos"
          :key="todo.id"
          class="todo-item"
          :class="{ completed: todo.completed }"
        >
          <div class="todo-content">
            <div class="checkbox" @click="toggleTodo(todo.id)">
              <span v-if="todo.completed" class="checkmark">✓</span>
            </div>
            <span class="todo-text">{{ todo.text }}</span>
          </div>
          <button @click="deleteTodo(todo.id)" class="delete-btn">×</button>
        </div>
      </TransitionGroup>
    </div>

    <div v-if="todos.length === 0" class="empty-state">
      <span class="empty-icon">📝</span>
      <p>暂无待办事项</p>
    </div>

    <div class="stats">
      共 {{ todos.length }} 项 | 已完成 {{ completedCount }} 项
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const todos = ref([])
const newTodo = ref('')

const completedCount = computed(() => todos.value.filter(t => t.completed).length)

onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  }
})

function saveTodos() {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

function addTodo() {
  const text = newTodo.value.trim()
  if (!text) return

  todos.value.push({
    id: Date.now(),
    text,
    completed: false
  })
  newTodo.value = ''
  saveTodos()
}

function toggleTodo(id) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) {
    todo.completed = !todo.completed
    saveTodos()
  }
}

function deleteTodo(id) {
  todos.value = todos.value.filter(t => t.id !== id)
  saveTodos()
}
</script>

<style scoped>
.container {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-radius: 24px;
  padding: 40px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  box-shadow:
    0 8px 32px rgba(0, 0, 0, 0.1),
    inset 0 1px 0 rgba(255, 255, 255, 0.4);
}

.title {
  color: white;
  text-align: center;
  margin-bottom: 30px;
  font-size: 32px;
  font-weight: 700;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.input-section {
  display: flex;
  gap: 12px;
  margin-bottom: 30px;
}

.input {
  flex: 1;
  padding: 16px 20px;
  font-size: 16px;
  border: none;
  border-radius: 12px;
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  outline: none;
  transition: all 0.3s ease;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

.input:focus {
  box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.5), inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

.add-btn {
  padding: 16px 28px;
  font-size: 16px;
  font-weight: 600;
  border: none;
  border-radius: 12px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
}

.add-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
}

.add-btn:active {
  transform: translateY(0);
}

.todo-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 20px;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
}

.todo-item:hover {
  background: rgba(255, 255, 255, 0.25);
  transform: translateX(4px);
}

.todo-item.completed {
  opacity: 0.6;
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
}

.todo-content {
  display: flex;
  align-items: center;
  gap: 16px;
}

.checkbox {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  flex-shrink: 0;
}

.checkbox:hover {
  border-color: #667eea;
  background: rgba(102, 126, 234, 0.2);
}

.todo-item.completed .checkbox {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-color: transparent;
}

.checkmark {
  color: white;
  font-size: 16px;
  font-weight: bold;
}

.todo-text {
  color: white;
  font-size: 16px;
}

.delete-btn {
  width: 32px;
  height: 32px;
  border: none;
  border-radius: 8px;
  background: rgba(255, 100, 100, 0.3);
  color: white;
  font-size: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.delete-btn:hover {
  background: rgba(255, 80, 80, 0.6);
  transform: scale(1.1);
}

.empty-state {
  text-align: center;
  padding: 40px;
  color: rgba(255, 255, 255, 0.7);
}

.empty-icon {
  font-size: 48px;
  display: block;
  margin-bottom: 16px;
}

.stats {
  text-align: center;
  margin-top: 24px;
  padding-top: 20px;
  border-top: 1px solid rgba(255, 255, 255, 0.2);
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
}

/* 过渡动画 */
.todo-enter-active,
.todo-leave-active {
  transition: all 0.4s ease;
}

.todo-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.todo-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>