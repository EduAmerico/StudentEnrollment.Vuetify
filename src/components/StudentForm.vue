<template>
  <v-container fluid class="py-0">
    <v-card class="form-card">
      <v-toolbar color="#3dbdc2" flat dense>
        <v-toolbar-title class="grey--text">{{ studentData ? 'Editar Aluno' : 'Cadastrar Aluno' }}</v-toolbar-title>
      </v-toolbar>
      <v-card-text>
        <v-form v-model="valid" ref="form">
          <v-text-field
            v-model="student.name"
            :rules="[v => !!v || 'Nome é obrigatório']"
            label="Nome"
            placeholder="Informe o nome completo"
            outlined
            dense
            required
          ></v-text-field>
          <v-text-field
            v-model="student.email"
            :rules="[v => !!v || 'E-mail é obrigatório']"
            label="E-mail"
            placeholder="Informe apenas um e-mail"
            outlined
            dense
            required
          ></v-text-field>
          <v-text-field
            v-model="student.ra"
            :rules="[v => !!v || 'RA é obrigatório']"
            label="RA"
            placeholder="Informe o registro acadêmico"
            outlined
            dense
            required
          ></v-text-field>
          <v-text-field
            v-model="student.cpf"
            :rules="[v => !!v || 'CPF é obrigatório']"
            label="CPF"
            placeholder="Informe o número do documento"
            outlined
            dense
            required
          ></v-text-field>
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-btn color="grey" @click="$emit('go-back')">Voltar</v-btn>
        <v-spacer></v-spacer>
        <v-btn color="grey" @click="resetForm">Cancelar</v-btn>
        <v-btn color="primary" @click="submitForm" :disabled="!valid">
          {{ studentData ? 'Atualizar' : 'Salvar' }}
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
export default {
  props: {
    studentData: Object // This prop receives the student to edit
  },
  data() {
    return {
      valid: false,
      student: {
        name: '',
        email: '',
        ra: '',
        cpf: ''
      }
    };
  },
  watch: {
    studentData: {
      immediate: true,
      handler(newValue) {
        if (newValue) {
          this.student = { ...newValue }; // Clone the data to avoid mutating the prop
        }
      }
    }
  },
  methods: {
    resetForm() {
      this.student = { name: '', email: '', ra: '', cpf: '' };
      this.$refs.form.resetValidation();
    },
    async submitForm() {
      if (this.$refs.form.validate()) {
        if (this.studentData) {
          this.$emit('student-updated', this.student); // Emit updated student data
        } else {
          try {
            const response = await fetch('http://localhost:5155/api/students', {
              method: 'POST',
              headers: {
                'Content-Type': 'application/json'
              },
              body: JSON.stringify(this.student)
            });

            if (response.ok) {
              this.$emit('student-added', await response.json());
              this.resetForm(); // Reset form
            } else {
              console.error('Failed to add student');
            }
          } catch (error) {
            console.error('Error submitting form:', error);
          }
        }
      }
    }
  }
};
</script>

<style scoped>
.form-card {
  max-width: 700px;
  margin: 20px auto;
  background-color: #c73d3d;
  border-radius: 10px;
  border: 1px solid #e0e0e0;
}

.v-text-field input {
  background-color: #ffffff;
}

.v-toolbar-title {
  font-weight: bold;
  font-size: 18px;
}
</style>
