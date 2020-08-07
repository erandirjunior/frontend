<template>
  <div>
    <b-card>
      <b-container fluid class="text-dark">
        <h1 class="col-12 text-info">{{ getTitle() }}</h1>
        <hr>
        <b-alert variant="danger" show v-for="(error, index) in errors" :key="index">{{ error }}</b-alert>
        <Form :object="form" @onSubmit="onSubmit"/>
        <b-modal id="modal-1" title="BootstrapVue" hide-footer>
          <h3>Cliente cadastrado com sucesso!</h3>
          <p class="my-4">Deseja cadastrar outro cliente?</p>
          <b-row>
            <b-col cols="2">
              <b-button @click="$bvModal.hide('modal-1')" variant="primary">SIM</b-button>
            </b-col>
            <b-col cols="2">
              <b-button to="/" variant="danger">NÃO</b-button>
            </b-col>
          </b-row>
        </b-modal>
        <b-modal id="modal-2" title="BootstrapVue" hide-footer>
          <h3>Informações atualizadas com sucesso!</h3>
          <b-button to="/" variant="primary">Fechar</b-button>
        </b-modal>
      </b-container>
    </b-card>
  </div>
</template>

<script>
import { post, get, put } from '@/components/infrastructure/request'
import Form from '@/components/view/Form'

export default {
  data () {
    return {
      form: {
        name: '',
        typePerson: '1',
        identifier: '',
        contacts: []
      },
      errors: []
    }
  },
  props: {
    id: {
      type: String
    }
  },
  components: {
    Form
  },
  methods: {
    getTitle () {
      if (this.id) {
        return 'Editando informações'
      }
      return 'Formulário de Cadastro'
    },
    onSubmit (evt) {
      this.id ? this.update(evt) : this.create(evt)
    },
    getInformation () {
      return {
        name: this.form.name,
        typePerson: this.form.typePerson,
        contacts: this.clearPhoneContacts(),
        identifier: this.clearIdentifier()
      }
    },
    formatCPF (cpf) {
      return cpf.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, '$1.$2.$3-$4')
    },
    formatCNPJ (cnpj) {
      return cnpj.replace(/(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})/g, '$1.$2.$3\\$4-$5')
    },
    clearPhoneContacts () {
      this.form.contacts.forEach((item, index) => {
        if (['1', '3'].includes(item.type)) {
          this.form.contacts[index].contact = item.contact
            .replace('(', '')
            .replace(')', '')
            .replace(' ', '')
            .replace(' ', '')
            .replace('-', '')
        }
      })

      return this.form.contacts
    },
    clearIdentifier () {
      return this.form.identifier
        .replace('.', '')
        .replace('.', '')
        .replace('.', '')
        .replace('/', '')
        .replace('-', '')
    },
    create (evt) {
      this.form = evt

      post('', this.getInformation())
        .then(() => {
          this.form.name = ''
          this.form.typePerson = '1'
          this.form.identifier = ''
          this.errors = []
          this.$bvModal.show('modal-1')
        })
        .catch(error => {
          this.errors = error.response.data.msg
        })
    },
    update (evt) {
      this.form = evt

      put(`/${this.id}`, this.getInformation())
        .then(() => {
          this.form.name = ''
          this.form.typePerson = '1'
          this.form.identifier = ''
          this.$bvModal.show('modal-2')
        })
        .catch(error => {
          this.errors = error.response.data.msg
        })
    },
    load () {
      get(`/${this.id}`)
        .then(response => {
          this.form = response

          if (this.form.typePerson === '1') {
            this.form.identifier = this.formatCPF(this.form.identifier)
          }

          if (this.form.typePerson === '2') {
            this.form.identifier = this.formatCNPJ(this.form.identifier)
          }
        })
    }
  },
  created () {
    if (this.id) {
      this.load()
    }
  }
}
</script>
