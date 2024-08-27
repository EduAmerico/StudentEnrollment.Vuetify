<template>
  <v-container fluid class="py-0">
    <v-card class="list-card">
      <v-toolbar color="#3dbdc2" flat dense>
        <v-toolbar-title class="grey--text">Consulta de Alunos</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          placeholder="Digite sua busca"
          append-icon="mdi-magnify"
          outlined
          dense
        ></v-text-field>
      </v-toolbar>
      <v-data-table
        :headers="headers"
        :items="filteredStudents"
        :items-per-page="5"
        class="elevation-1"
        dense
      >
        <template v-slot:item.actions="{ item }">
          <v-btn icon small color="primary" @click="$emit('edit-student', item)">
            <v-icon>mdi-pencil</v-icon>
          </v-btn>
          <v-btn icon small color="red" @click="$emit('delete-student', item.id)">
            <v-icon>mdi-delete</v-icon>
          </v-btn>
        </template>
      </v-data-table>
      <v-card-actions>
        <v-btn color="white" @click="$emit('go-back')">Voltar</v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
export default {
  props: ['students'],
  data() {
    return {
      search: '',
      headers: [
        { text: 'Registro Acadêmico', value: 'ra', sortable: true },
        { text: 'Nome', value: 'name', sortable: true },
        { text: 'CPF', value: 'cpf', sortable: true },
        { text: 'Ações', value: 'actions', sortable: false }
      ]
    };
  },
  computed: {
    filteredStudents() {
      const searchLower = this.search.toLowerCase();
      return this.students.filter(student => {
        return (
          student.name.toLowerCase().includes(searchLower) ||
          student.ra.includes(searchLower) ||
          student.cpf.includes(searchLower)
        );
      });
    }
  }
};
</script>

<style scoped>
.list-card {
  max-width: 900px;
  margin: 20px auto;
  background-color: rgb(179, 52, 52);
  border-radius: 10px;
  border: 1px solid #e0e0e0;
}

.v-toolbar-title {
  font-weight: bold;
  font-size: 18px;
}
</style>
