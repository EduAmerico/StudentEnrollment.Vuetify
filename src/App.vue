<template>
  <v-app>
    <!-- Navigation Bar -->
    <v-app-bar app flat class="app-bar">
      <v-row align="center" no-gutters>
        <!-- Logo -->
        <v-col cols="2" class="logo">
          <v-img src="@/assets/logo.png" alt="Logo" contain></v-img>
        </v-col>

        <!-- Spacer to push buttons to the right -->
        <v-spacer></v-spacer>

        <!-- Buttons to switch sections -->
        <v-col cols="3" class="text-right">
          <v-btn color="primary" @click="showForm" class="mr-2">Cadastrar Aluno</v-btn>
          <v-btn color="primary" @click="showList">Consultar Alunos</v-btn>
        </v-col>
      </v-row>
    </v-app-bar>

    <!-- Content Area -->
    <v-main>
      <v-container fluid class="content-area">
        <v-card class="content-card">
          <student-form
            v-if="currentView === 'form'"
            :student-data="selectedStudent"
            @student-added="fetchStudents"
            @go-back="showList"
            @student-updated="updateStudent"
          ></student-form>
          <student-list
            v-if="currentView === 'list'"
            :students="students"
            @go-back="showForm"
            @edit-student="editStudent"
            @delete-student="deleteStudent"
          ></student-list>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import StudentForm from './components/StudentForm.vue';
import StudentList from './components/StudentList.vue';

export default {
  components: {
    StudentForm,
    StudentList
  },
  data() {
    return {
      currentView: 'list', // Default view to show the student list
      students: [],
      selectedStudent: null, // Holds the student being edited
    };
  },
  methods: {
    showForm() {
      this.selectedStudent = null; // Reset selectedStudent when opening the form
      this.currentView = 'form';
    },
    showList() {
      this.currentView = 'list';
    },
    async fetchStudents() {
      try {
        const response = await fetch('http://localhost:5155/api/students');
        this.students = await response.json();
      } catch (error) {
        console.error('Error fetching students:', error);
      }
    },
    editStudent(student) {
      this.selectedStudent = student; // Pass the selected student to the form
      this.currentView = 'form';
    },
    async deleteStudent(studentId) {
      try {
        const response = await fetch(`http://localhost:5155/api/students/${studentId}`, {
          method: 'DELETE',
        });
        if (response.ok) {
          this.fetchStudents(); // Refresh the student list after deletion
        } else {
          console.error('Failed to delete student');
        }
      } catch (error) {
        console.error('Error deleting student:', error);
      }
    },
    async updateStudent(updatedStudent) {
      try {
        const response = await fetch(`http://localhost:5155/api/students/${updatedStudent.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(updatedStudent)
        });
        if (response.ok) {
          this.fetchStudents(); // Refresh the list after update
        } else {
          console.error('Failed to update student');
        }
      } catch (error) {
        console.error('Error updating student:', error);
      }
    },
  },
  mounted() {
    this.fetchStudents();
  }
};
</script>

<style scoped>
/* Background image for the app */
.v-main {
  background-image: url('@/assets/background.png');
  background-size: cover;
  background-position: center;
  min-height: 100vh;
}

.app-bar {
  background-color: rgba(255, 255, 255, 0.8); /* Semi-transparent background */
  box-shadow: none;
}

.logo {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 110px;
  height: 110px;
}

.content-area {
  margin-top: 100px; /* Adjust for the height of the app-bar */
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.content-card {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 800px;
}
</style>
