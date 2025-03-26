<script setup lang="ts">
import { computed, ref } from 'vue'
import CheckBox from './components/CheckBox.vue'
// Définir le type pour une tâche avec types script
interface Todo {
  title: string
  completed: boolean
  date: number
}

const todos_Input = ref('')
// le todos_Input est pour la pour capturer l'entrer de l'utilisateur, je lui ais passer une ref car elle peut
// changer a tout moment docn en quelque sorte todos_Input est une variable dynamique car elle n'est pas figée

const todos = ref<Todo[]>([
  { title: 'Tâche par défaut', completed: true, date: Date.now() },
  { title: 'Tâche par défaut 1', completed: false, date: Date.now() },
]) // Utiliser le type Todo pour l'array de tâches

const Add_Task = () => {
  if (todos_Input.value.trim()) {
    // Vérifie si l'input n'est pas vide après suppression des espaces
    todos.value.push({
      title: todos_Input.value,
      completed: false, // La tâche est initialement marquée comme non complétée
      date: Date.now(),
    })
    todos_Input.value = '' // Réinitialise le champ de saisie après ajout
  }
}
const remaining_todos = computed(() => {
  return todos.value.filter((t) => t.completed === false).length
})

const Remove_Task = (index: number) => {
  todos.value.splice(index, 1)
}
// computed permet ici d'atendre les changments avec de reagir docn
// la fonction n'est pas appeler quand il n'y a pas de changement
// c'est lorsqu'on a besoin d'une valeur qui derive d'une autre valeur qui es elle meme
// dinamique on utilise la fonction Computed
const Todo_Sort = computed(() => {
  console.log('Todo_Sort')
  const Todo_Sort = [...todos.value].sort((a, b) => (a.completed > b.completed ? 1 : -1))
  if (Hide_Completed.value === true) {
    return Todo_Sort.filter((t) => t.completed === false)
  }
  return Todo_Sort
})

const Hide_Completed = ref(false)
</script>
<!-- ln twuiter  -->
<template>
  <form @submit.prevent="Add_Task">
    <fieldset role="group">
      <input type="text" placeholder="Name Of Task To Execute" v-model="todos_Input" required />
      <button :disabled="todos_Input.length === 0" type="submit">Add My Task</button>
    </fieldset>
    <fieldset title="print">
      <div v-if="todos.length === 0">Il n'y a pas de tâches</div>
      <ul v-else>
        <li
          v-for="(todo, index) in Todo_Sort"
          :key="todo.date"
          :class="{ completed: todo.completed }"
        >
          <label>
          

            
            <input type="checkbox" v-model="todo.completed" /> {{ todo.title }}
            <button @click="Remove_Task(index)">Supprimer</button>
          </label>
        </li>
      </ul>
      <label>
         <input type="checkbox" v-model="Hide_Completed" />
          MASK THE COMPLETED TASK
        </label>
      <p v-if="remaining_todos > 0">
        Il reste {{ remaining_todos }} tâche{{ remaining_todos > 1 ? 's' : ' ' }} à faire
      </p>
    </fieldset>
  </form>
</template>

<style>
.checkbox {
  color: #00ff0d;
}
.completed label {
  opacity: 0.5;
  text-decoration: line-through;
  color: #00ff0d;

  transition: color 0.3s ease-in-out;

  font-style: italic;
  font-weight: bold;
}
</style>
