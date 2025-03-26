<template>
  <form @submit.prevent="submitForm">
    <fieldset>
      <legend>Enter your task to accomplish</legend>

      <input type="text" placeholder="Enter your task here please" v-model="taskTitle" />
      <textarea placeholder="Enter description's task" v-model="taskDescription"></textarea>
      <button type="submit">Submit</button>
    </fieldset>
  </form>

  <fieldset>
    <legend>List of all tasks</legend>
    <table>
      <thead>
        <tr>
          <th>Title Number</th>
          <th>Title Task</th>
          <th>Description Task</th>
          <th>Date Task</th>
          <th>Del Task</th>
          <th>Edt Task</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="task.id">
          <td>Task N°{{ index + 1 }}</td>
          <td>{{ task.title }}</td>
          <td>{{ task.description }}</td>
          <td>{{ formatDate(task.date) }}</td>
          <!-- Affichage de la date formatée -->
          <td>
            <!-- Bouton de suppression de la tâche -->
            <button @click="deleteTask(task.id)">Delete Task</button>
          </td>
          <td>
            <!-- Bouton de modification de la tâche -->
            <button @click="editTask(task)">Edit Task</button>
          </td>
        </tr>
      </tbody>
    </table>
  </fieldset>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// Variables liées aux données du formulaire
const taskTitle = ref('')
const taskDescription = ref('')
const tasks = ref<
{ id: number; 
  title: string; 
  description: 
  string; date:  
string 
}[]>([])
const taskToEdit = ref<{ 
  id: number | null; 
  title: string; 
  description: string 
} | null>(null)

// Fonction pour formater la date
const formatDate = (timestamp: string) => {
  const parsedDate = new Date(Number(timestamp)) // Convertir le timestamp en date
  if (isNaN(parsedDate.getTime())) {
    return '' // Retourne une chaîne vide si la date est invalide
  }
  return new Intl.DateTimeFormat('fr-FR', {
    weekday: 'long', // Jour de la semaine (ex: lundi)
    year: 'numeric', // Année (ex: 2025)
    month: 'long', // Mois (ex: mars)
    day: 'numeric', // Jour du mois (ex: 26)
  }).format(parsedDate)
}

// Fonction pour soumettre le formulaire (ajouter ou modifier une tâche)
const submitForm = () => {
  if (!taskTitle.value.trim() || !taskDescription.value.trim()) {
    alert('Please enter both title and description')
    return // Annule le traitement si les champs ne sont pas remplis
  }

  const currentDate = Date.now().toString() // Récupère le timestamp actuel (en millisecondes)

  if (taskToEdit.value) {
    const task = tasks.value.find(t => t.id === taskToEdit.value.id)
    if (task) {
      task.title = taskTitle.value
      task.description = taskDescription.value
      task.date = currentDate // Mise à jour de la date
    }
    taskToEdit.value = null // Réinitialiser l'édition
  } else {
    // Ajoute la tâche à la liste avec la date actuelle
    tasks.value.push({
      id: Date.now(), // Utilisation de Date.now() pour générer un identifiant unique
      title: taskTitle.value,
      description: taskDescription.value,
      date: currentDate,
    })
  }

  // Réinitialiser les champs du formulaire après soumission
  taskTitle.value = ''
  taskDescription.value = ''
}

// Fonction pour modifier une tâche
const editTask = (task: { id: number; title: string; description: string; date: string }) => {
  taskTitle.value = task.title
  taskDescription.value = task.description
  taskToEdit.value = { id: task.id, title: task.title, description: task.description } // Conserve l'ID de la tâche à éditer
}

// Fonction pour supprimer une tâche
const deleteTask = (taskId: number) => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId) // Supprime la tâche de la liste
}
</script>

<style scoped>
/* Style pour visualiser le contour du fieldset */
fieldset {
  border: 2px solid #4caf50; /* Bordure verte */
  padding: 20px;
  margin: 20px 0;
}

legend {
  font-weight: bold;
  font-size: 1.2em;
  color: #4caf50; /* Couleur du texte du titre */
  padding: 0 10px; /* Pour ajouter un peu d'espace autour du titre */
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 10px;
  text-align: left;
  border: 1px solid #ddd;
}

th {
  background-color: #4caf50;
  color: white;
}

tbody tr:nth-child(even) {
  background-color: #f2f2f2;
}
</style>
