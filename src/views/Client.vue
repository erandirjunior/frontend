<template>
  <div>
    <b-card>
      <b-container fluid class="text-dark">
        <h3 class="col-12">Título</h3>
        <hr>
        <b-form class="col-12" @submit="onSubmit" @reset="onReset">

          <b-row>
            <b-col cols="12">
              <b-form-group id="input-group-2" label="Nome do Cliente" label-for="input-2">
                <b-form-input
                  id="input-2"
                  v-model="form.name"
                  required
                  placeholder="Nome do Cliente"
                ></b-form-input>
              </b-form-group>
            </b-col>

            <b-col cols="3">
              <b-form-group label="Selecione o tipo do cliente:">
                <b-form-radio v-model="form.typePerson" name="some-radios" value="1">Pessoa Física</b-form-radio>
                <b-form-radio v-model="form.typePerson" name="some-radios" value="2">Pessoa Jurídica</b-form-radio>
              </b-form-group>
            </b-col>

            <b-col cols="9">
              <b-form-group label="Digite o CPF do Cliente" label-for="input-2" v-if="form.typePerson === '1'">
                <b-form-input
                  v-model="form.identifier"
                  required
                  placeholder="CPF do Cliente"
                  v-mask="'###.###.###-##'"
                ></b-form-input>
              </b-form-group>

              <b-form-group label="Digite o CNPJ do Cliente" label-for="input-2" v-else>
                <b-form-input
                  v-model="form.identifier"
                  required
                  placeholder="CNPJ do Cliente"
                  v-mask="'##.###.###/####.##'"
                ></b-form-input>
              </b-form-group>
            </b-col>
          </b-row>

          <b-button type="submit" variant="primary">Enviar</b-button>
        </b-form>
      </b-container>
    </b-card>
  </div>
</template>

<script>
import { mask } from 'vue-the-mask'

export default {
  data () {
    return {
      form: {
        name: '',
        typePerson: '1',
        identifier: ''
      }
    }
  },
  methods: {
    onSubmit (evt) {
      evt.preventDefault()
      alert(JSON.stringify(this.form))
    },
    onReset (evt) {
      evt.preventDefault()
      // Reset our form values
      this.form.email = ''
      this.form.name = ''
      this.form.food = null
      this.form.checked = []
      // Trick to reset/clear native browser form validation state
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
    }
  },
  directives: { mask }
}
</script>
