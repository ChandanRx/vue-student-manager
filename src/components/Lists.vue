<script setup>
import { ref, onMounted, watch } from 'vue';

const students = ref([]);

// Load students from localStorage on mount
onMounted(() => {
  const saved = localStorage.getItem('students');
  if (saved) {
    students.value = JSON.parse(saved);
  } else {
    students.value = [
      { id: 1, name: 'Pengu', class: '12E', grade: 'A', section: 'Yellow', attendance: 100 },
      { id: 2, name: 'Coonie', class: '12E', grade: 'A', section: 'Yellow', attendance: 100 },
    ];
  }
});


watch(students, (newVal) => {
  localStorage.setItem('students', JSON.stringify(newVal));
}, { deep: true });

const showModal = ref(false);
const isEditing = ref(false);
const form = ref({
  id: null,
  name: '',
  class: '',
  grade: '',
  section: '',
  attendance: ''
});

const openAddForm = () => {
  form.value = { id: null, name: '', class: '', grade: '', section: '', attendance: '' };
  isEditing.value = false;
  showModal.value = true;
};

const openEditForm = (student) => {
  form.value = { ...student };
  isEditing.value = true;
  showModal.value = true;
};

const saveStudent = () => {
  if (isEditing.value) {
    const index = students.value.findIndex(s => s.id === form.value.id);
    if (index !== -1) students.value[index] = { ...form.value };
  } else {
    const newId = Date.now();
    students.value.push({ ...form.value, id: newId });
  }
  showModal.value = false;
};

const deleteStudent = (id) => {
  students.value = students.value.filter(s => s.id !== id);
};
</script>

<template>
  <section class="bg-gradient-to-b from-slate-200 to-slate-300 min-h-screen p-4 md:p-10">
    <!-- Heading -->
    <h1 class="text-3xl md:text-5xl font-extrabold text-center text-gray-800 mb-6">
      📚 Students List
    </h1>

    <div class="mb-6 flex justify-end">
      <button @click="openAddForm" class="bg-blue-600 hover:bg-blue-700 text-white px-5 py-2 rounded-lg shadow-md transition-all">
        ➕ Add Student
      </button>
    </div>

    <div class="overflow-x-auto rounded-lg shadow-md bg-white">
      <table class="min-w-full text-sm md:text-base">
        <thead class="bg-blue-600 text-white">
          <tr>
            <th class="px-4 py-3 text-left">#</th>
            <th class="px-4 py-3 text-left">Name</th>
            <th class="px-4 py-3 text-left">Class</th>
            <th class="px-4 py-3 text-left">Grade</th>
            <th class="px-4 py-3 text-left">Section</th>
            <th class="px-4 py-3 text-left">Attendance %</th>
            <th class="px-4 py-3 text-left">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(student, index) in students" :key="student.id" class="border-b hover:bg-gray-50 transition">
            <td class="px-4 py-3">{{ index + 1 }}</td>
            <td class="px-4 py-3 font-medium text-gray-800">{{ student.name }}</td>
            <td class="px-4 py-3">{{ student.class }}</td>
            <td class="px-4 py-3">{{ student.grade }}</td>
            <td class="px-4 py-3">{{ student.section }}</td>
            <td class="px-4 py-3">{{ student.attendance }}%</td>
            <td class="px-4 py-3 flex gap-2">
              <button @click="openEditForm(student)" class="text-blue-500 hover:text-blue-700 text-xl">✏️</button>
              <button @click="deleteStudent(student.id)" class="text-red-500 hover:text-red-700 text-xl">🗑️</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal -->
    <div v-if="showModal" class="fixed inset-0 bg-black bg-opacity-40 flex items-center justify-center px-4 z-50">
      <div class="bg-white w-full max-w-lg p-6 rounded-lg shadow-lg overflow-y-auto max-h-[90vh]">
        <h2 class="text-xl font-bold mb-4 text-center">
          {{ isEditing ? '✏️ Edit' : '➕ Add' }} Student
        </h2>
        <form @submit.prevent="saveStudent" class="space-y-4">
          <input v-model="form.name" type="text" placeholder="Full Name" class="w-full p-3 border border-gray-300 rounded-lg" required />
          <input v-model="form.class" type="text" placeholder="Class" class="w-full p-3 border border-gray-300 rounded-lg" required />
          <input v-model="form.grade" type="text" placeholder="Grade" class="w-full p-3 border border-gray-300 rounded-lg" required />
          <input v-model="form.section" type="text" placeholder="Section" class="w-full p-3 border border-gray-300 rounded-lg" required />
          <input v-model="form.attendance" type="number" placeholder="Attendance %" class="w-full p-3 border border-gray-300 rounded-lg" required />

          <div class="flex justify-end gap-3 pt-2">
            <button type="button" @click="showModal = false" class="bg-gray-400 hover:bg-gray-500 text-white px-4 py-2 rounded-lg">
              Cancel
            </button>
            <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white px-5 py-2 rounded-lg">
              {{ isEditing ? 'Update' : 'Add' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>

<style scoped>
html, body {
  font-family: 'Segoe UI', sans-serif;
}
</style>
