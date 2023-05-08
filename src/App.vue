<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const notes = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const notes_asc = computed(() => notes.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}));

const addNote = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return console.log("note created")
	}
	notes.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeNote = (note) => {
  notes.value = notes.value.filter((n) => n !== note)
}


watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
});

watch(notes, (newVal) => {
	localStorage.setItem('notes', JSON.stringify(newVal))
}, {
	deep: true
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  notes.value = JSON.parse(localStorage.getItem('notes')) || []

});

</script>

<template>
  <main class="app">

    <section class="greeting">
        <h2 class="title">
          Welcome, <input class="name-input" style="font-size:25px;" type="text" placeholder="Name here" v-model="name" /> 
        </h2>
    </section>
  
  
    <section class="create-note">
    
      <h4>Create a note</h4>
      <h6>After creating a note, select which category it belongs to. Notes will be color coordinated with the selected category</h6>
    
    <form id="new-note" @submit.prevent="addNote">
      <div class="note-container">
        <input type="text" placeholder="e.g. execute manual tests for MealZ" class="note-card" v-model="input_content" />
          <div class="cat-container">
        <label>
          <input type="radio"
                 name="category"
                 value="tech"
                 v-model="input_category" />
                 <span class="bubble tech"></span>
                 <div class="cat-heading">Tech</div>
        </label>

        <label>
          <input type="radio"
                 name="category"
                 value="food"
                 v-model="input_category" />
                 <span class="bubble food"></span>
                 <div class="cat-heading">Food</div>

        </label>

        <label>
          <input type="radio"
                 name="category"
                 value="fitness"
                 v-model="input_category" />
                 <span class="bubble fitness"></span>
                 <div class="cat-heading">Fitness</div>
        </label>

        <label>
          <input type="radio"
                 name="category"
                 value="other"
                 v-model="input_category" />
                 <span class="bubble other"></span>
                 <div class="cat-heading">Other</div>
        </label>
        </div>
        </div>
        <input type="submit" value="Add Note" class="btn" />
      </form>
    </section>

    <section class="note-container">
      <h3>Notes</h3>
      <div class="container" id="note-container">
        <div v-for="note in notes_asc" :class="`note-item ${note.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="note.done" />
						<span :class="`bubble ${note.category}`"></span>
					</label>

					<div class="note-content">
						<input type="text" v-model="note.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeNote(note)">Delete</button>
					</div>
				</div>
      </div>

    </section>



  </main>
</template>

