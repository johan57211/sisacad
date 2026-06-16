<template>
  <div class="constancia">
    <div v-if="loading">Cargando...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <h1>CONSTANCIA DE MATRÍCULA DE LABORATORIO</h1>
      <h2>Escuela Profesional de Ingeniería de Sistemas EPIS</h2>
      <p class="emitido">Emitido el: {{ fechaEmision }}</p>
      <hr />

      <section>
        <div class="seccion-titulo">DATOS DEL ALUMNO</div>
        <div class="datos-alumno">
          <p><strong>C.U.I.:</strong> {{ student.cui }}</p>
          <p><strong>Nombre completo:</strong> {{ student.full_name }}</p>
          <p><strong>Email:</strong> {{ student.email }}</p>
        </div>
      </section>

      <section style="margin-top: 20px;">
        <div class="seccion-titulo">ASIGNATURAS MATRICULADAS</div>
        <table>
          <thead>
            <tr>
              <th>N°</th>
              <th>Código</th>
              <th>Curso</th>
              <th>Año</th>
              <th>Grupo</th>
              <th>Laboratorio</th>
              <th>Docente</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(enrollment, index) in enrollments" :key="enrollment.id">
              <td>{{ index + 1 }}</td>
              <td>{{ enrollment.workload.course.code }}</td>
              <td>
                <strong>{{ enrollment.workload.course.name }}</strong><br/>
                ({{ enrollment.workload.course.acronym }})
              </td>
              <td>{{ enrollment.workload.course.year_display }}</td>
              <td>{{ enrollment.workload.group }}</td>
              <td>{{ enrollment.workload.laboratory }}</td>
              <td>{{ enrollment.workload.teacher.full_name }}</td>
            </tr>
          </tbody>
        </table>
        <p class="total"><strong>Total de cursos matriculados: {{ enrollments.length }}</strong></p>
      </section>

      <footer>
        <em>Documento generado digitalmente por el Sistema de Matrícula de Laboratorio EPIS.</em>
      </footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'ConstanciaView',
  data() {
    return {
      loading: true,
      error: null,
      student: {},
      enrollments: [],
      fechaEmision: ''
    }
  },
  created() {
    const cui = this.$route.params.cui
    this.fechaEmision = new Date().toLocaleDateString('es-PE')
    this.fetchConstancia(cui)
  },
  methods: {
    async fetchConstancia(cui) {
      try {
        const response = await axios.get(
          `${import.meta.env.VITE_API_BASE_URL}/restful/enrollment-certificate/?cui=${cui}`
        )
        const results = response.data.results
        this.student = results[0].student
        this.enrollments = results
      } catch (err) {
        console.log(err)
        this.error = 'No se pudo obtener la constancia.'
      } finally {
        this.loading = false
      }
    }
  }
}
</script>

<style scoped>
* {
  font-family: Arial, sans-serif;
  box-sizing: border-box;
}

.constancia {
  max-width: 900px;
  margin: 40px auto;
  padding: 30px;
  border: 1px solid #ccc;
}

h1 {
  text-align: center;
  color: #1a5276;
  font-size: 1.2rem;
  margin-bottom: 5px;
}

h2 {
  text-align: center;
  font-size: 1rem;
  font-weight: normal;
  margin-bottom: 5px;
}

p.emitido {
  text-align: center;
  font-size: 0.9rem;
  color: #555;
}

hr {
  margin: 20px 0;
  border: none;
  border-top: 1px solid #ccc;
}

.seccion-titulo {
  background-color: #d6e4f0;
  padding: 8px 12px;
  font-weight: bold;
  font-size: 0.95rem;
  border-left: 4px solid #1a5276;
  margin-bottom: 12px;
}

.datos-alumno p {
  margin: 6px 0;
  font-size: 0.95rem;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
  font-size: 0.9rem;
}

thead tr {
  background-color: #f2f2f2;
}

th, td {
  border: 1px solid #ccc;
  padding: 8px 10px;
  text-align: left;
}

.total {
  margin-top: 12px;
  font-size: 0.95rem;
}

footer {
  margin-top: 30px;
  text-align: center;
  font-size: 0.8rem;
  color: #777;
}
</style>