<template>
  <div>
    <b-card>
      <b-container fluid class="text-dark">
        <h3 class="col-12 text-info font-weight-bold">Gerenciador de Clientes</h3>
        <hr>

        <div class="col-12">
          <b-row>
            <b-col cols="sm-12 col-md-6">
              <b-form-group class="mr-auto">
                <b-input-group size="sm" >
                  <b-form-input
                    v-model="filter"
                    type="search"
                    id="filterInput"
                    placeholder="Pesquisar..."
                  ></b-form-input>
                  <b-input-group-append>
                    <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
                  </b-input-group-append>
                </b-input-group>
              </b-form-group>
            </b-col>

            <b-col cols="sm-12 col-md-6">
              <b-button class="float-lg-right" variant="info" to="/about" size="sm">Registrar Novo Cliente</b-button>
            </b-col>

            <b-col cols="sm-12 col-md-12">
              <b-table
                sort-icon-left
                striped
                bordered
                fixed
                responsive="sm"
                head-variant="dark"
                :items="items"
                :fields="fields"
                :filter="filter"
              >
                <template v-slot:cell(ações)="row">
                  <b-button @click="row.toggleDetails" class="mr-2 btn-info">
                    <b-icon icon="eye"></b-icon>
                  </b-button>

                  <b-button :to="`/${row.item.id}`" class="mr-2 btn-warning">
                    <b-icon icon="pencil"></b-icon>
                  </b-button>

                  <b-button @click="remove(row.item.id)" class="mr-2 btn-danger">
                    <b-icon icon="trash"></b-icon>
                  </b-button>
                </template>

                <template v-slot:row-details="row">
                  <Form :object="row.item" seeOnly="' '"/>
                </template>
              </b-table>
            </b-col>
          </b-row>
        </div>
      </b-container>
    </b-card>
  </div>
</template>

<script>
// @ is an alias to /src
import { get, del } from '@/components/infrastructure/request'
import Form from '@/components/view/Form'

export default {
  name: 'Home',
  data () {
    return {
      fields: [
        {
          key: 'name',
          label: 'Nome do Cliente',
          sortable: true
        },
        {
          key: 'identifier',
          label: 'Identificador',
          sortable: true
        },
        {
          key: 'ações'
        }
      ],
      items: [],
      filter: null
    }
  },
  components: {
    Form
  },
  methods: {
    remove (id) {
      del(`/${id}`)
        .then(() => {
          this.load()
        })
    },
    formatCPF (cpf) {
      return cpf.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, '$1.$2.$3-$4')
    },
    formatCNPJ (cnpj) {
      return cnpj.replace(/(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})/g, '$1.$2.$3\\$4-$5')
    },
    load () {
      get()
        .then(response => {
          this.items = []
          response.forEach(item => {
            item.identifier = item.identifier.length === 11
              ? this.formatCPF(item.identifier)
              : this.formatCNPJ(item.identifier)

            this.items.push(item)
          })
        })
    }
  },
  created () {
    this.load()
  }
}
</script>
