<template>
  <!-- Formulaire pour ajouter ou modifier une tâche -->
  <form @submit.prevent="submitForm">
    <fieldset>
      <legend>Enter your task to accomplish</legend>

      <!-- Champ pour entrer le titre de la tâche -->
      <input type="text" placeholder="Enter your task here please" v-model="taskTitle" />

      <!-- Champ pour entrer la description de la tâche -->
      <textarea placeholder="Enter description's task" v-model="taskDescription"></textarea>

      <!-- Bouton de soumission du formulaire -->
      <button type="submit">Submit</button>
    </fieldset>
  </form>

  <!-- Affichage de la liste des tâches -->
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

          <!-- Bouton pour supprimer la tâche -->
          <td>
            <button @click="deleteTask(task.id)">Delete Task</button>
          </td>

          <!-- Bouton pour modifier la tâche -->
          <td>
            <button @click="editTask(task)">Edit Task</button>
          </td>
        </tr>
      </tbody>
    </table>
  </fieldset>
</template>

<script setup lang="ts">
import { ref } from 'vue'


// je veux que les utilisateur n'entrent que des phrses
// correctement orthographiées de leur tâches
//  et aussi a la soumission du formulaire je dois verifier qu'ils
// respecte ma condition 



// Déclaration des variables réactives avec `ref` pour stocker les valeurs dynamiques
const taskTitle = ref('') // Stocke le titre de la tâche en cours de saisie
const taskDescription = ref('') // Stocke la description de la tâche en cours de saisie

// Liste réactive des tâches stockant les objets des tâches
const tasks = ref<{ id: number; title: string; description: string; date: string }[]>([])

// Variable qui stocke la tâche en cours de modification
const taskToEdit = ref<{ id: number | null; title: string; description: string } | null>(null)

// Fonction pour formater une date en une chaîne lisible
const formatDate = (timestamp: string) => {
  const parsedDate = new Date(Number(timestamp)) // Convertit le timestamp en objet Date

  if (isNaN(parsedDate.getTime())) {
    // NaN : signifie Not a Nunber
    return '' // Retourne une chaîne vide si la conversion échoue
  }
  // Intl.DateTimeFormat fait partie de l'API d'internationalisation
  // (Intl) de JavaScript. Il permet d'afficher une date dans un format
  // adapté à une langue donnée.
  return new Intl.DateTimeFormat('fr-FR', {
    // Option permettant d'afficher le jour de la semaine en toutes lettres
    // Exemple : "lundi", "mardi", "mercredi"
    weekday: 'long', // Jour de la semaine
    // Option pour afficher l'année sous forme de nombre à 4 chiffres
    // Exemple : "2025"
    year: 'numeric', // Année
    // Option pour afficher le mois en toutes lettres
    // Exemple : "janvier", "février", "mars"
    month: 'long', // Mois
    // Option pour afficher le numéro du jour dans le mois
    // Exemple : "1", "15", "30"
    day: 'numeric', // Jour du mois
    timeZoneName: 'short', // Affiche le fuseau horaire (ex: CET, GMT)
  }).format(parsedDate)
}

// Fonction pour soumettre le formulaire (ajouter ou modifier une tâche)
const submitForm = () => {
  // Vérification que les champs ne sont pas vides
  if (!taskTitle.value.trim() || !taskDescription.value.trim()) {
    alert('Please enter both title and description')
    return // Interrompt la fonction si les champs sont vides
  }

  const currentDate = Date.now().toString() // Génère un timestamp pour la date actuelle

  if (taskToEdit.value) {
    // Si une tâche est en cours de modification, on met à jour ses valeurs
    const task = tasks.value.find((t) => t.id === taskToEdit.value.id)
    if (task) {
      task.title = taskTitle.value
      task.description = taskDescription.value
      task.date = currentDate // Mise à jour de la date
    }
    taskToEdit.value = null // Réinitialise le mode édition
  } else {
    // Ajout d'une nouvelle tâche si aucune tâche n'est en édition
    tasks.value.push({
      id: Date.now(), // Génère un ID unique en utilisant le timestamp
      title: taskTitle.value,
      description: taskDescription.value,
      date: currentDate,
    })
  }

  // Réinitialisation des champs après soumission
  taskTitle.value = ''
  taskDescription.value = ''
}

// Fonction pour passer en mode édition d'une tâche spécifique
const editTask = (task: { id: number; title: string; description: string; date: string }) => {
  taskTitle.value = task.title // Remplit le champ titre avec la valeur existante
  taskDescription.value = task.description // Remplit le champ description avec la valeur existante
  taskToEdit.value = { id: task.id, title: task.title, description: task.description } // Stocke l'ID de la tâche à éditer
}

// Fonction pour supprimer une tâche en filtrant la liste des tâches
const deleteTask = (taskId: number) => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId) // Supprime la tâche en fonction de son ID
}
</script>

<style scoped>
/* Style du formulaire et de la liste des tâches */
fieldset {
  border: 2px solid #4caf50; /* Bordure verte */
  padding: 20px;
  margin: 20px 0;
}

legend {
  font-weight: bold;
  font-size: 1.2em;
  color: #4caf50; /* Couleur verte */
  padding: 0 10px;
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
