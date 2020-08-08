<template>
  <b-form class="col-12" @submit.prevent="onSubmit">
    <b-row>
      <b-col cols="sm-12 col-md-12">
        <h2>Informações Pessoais</h2>
      </b-col>
    </b-row>
    <b-row>
      <b-col cols="sm-12 col-md-12">
        <b-form-group id="input-group-2" label="Nome do Cliente" label-for="input-2">
          <b-form-input
            :disabled="!!seeOnly"
            id="input-2"
            v-model="object.name"
            placeholder="Nome do Cliente"
            required
          ></b-form-input>
        </b-form-group>
      </b-col>

      <b-col cols="sm-12 col-md-3">
        <b-form-group label="Selecione o tipo do cliente:">
          <b-form-radio :disabled="!!seeOnly" v-model="object.typePerson" name="some-radios" value="1">Pessoa Física</b-form-radio>
          <b-form-radio :disabled="!!seeOnly" v-model="object.typePerson" name="some-radios" value="2">Pessoa Jurídica</b-form-radio>
        </b-form-group>
      </b-col>

      <b-col cols="sm-12 col-md-9">
        <b-form-group label="Digite o CPF do Cliente" label-for="input-2" v-if="object.typePerson === '1'">
          <b-form-input
            v-model="object.identifier"
            required
            :disabled="!!seeOnly"
            placeholder="CPF do Cliente"
            v-mask="'###.###.###-##'"
          ></b-form-input>
        </b-form-group>

        <b-form-group label="Digite o CNPJ do Cliente" label-for="input-2" v-else>
          <b-form-input
            v-model="object.identifier"
            required
            :disabled="!!seeOnly"
            placeholder="CNPJ do Cliente"
            v-mask="'##.###.###/####-##'"
          ></b-form-input>
        </b-form-group>
      </b-col>
    </b-row>

    <b-row>
      <b-col cols="sm-12 col-md-2">
        <h2>Contatos</h2>
      </b-col>
      <b-col cols="3" v-if="!seeOnly">
        <b-button @click="addContact" variant="outline-success" size="small">Adicionar Contato</b-button>
      </b-col>
    </b-row>

    <b-row v-for="(contacts, index) in object.contacts" :key="index">
      <b-col v-if="!seeOnly" cols="sm-12 col-md-2">
        <br>
        <b-button @click="remove(index)" class="mr-2 btn-danger">
          Excluir Contato
        </b-button>
      </b-col>

      <b-col cols="sm-12 col-md-3">
        <b-form-group label="Selecione o tipo do cliente:">
          <b-form-radio :disabled="!!seeOnly" v-model="object.contacts[index].type" @change="clearField(index)" value="1">Celular</b-form-radio>
          <b-form-radio :disabled="!!seeOnly" v-model="object.contacts[index].type" @change="clearField(index)" value="2">E-mail</b-form-radio>
          <b-form-radio :disabled="!!seeOnly" v-model="object.contacts[index].type" @change="clearField(index)" value="3">Telefone</b-form-radio>
        </b-form-group>
      </b-col>

      <b-col cols="sm-12 col-md-7">
        <b-form-group label="Celular do Cliente" v-if="object.contacts[index].type === '1'">
          <b-form-input
            v-model="object.contacts[index].contact"
            :disabled="!!seeOnly"
            required
            placeholder="Celular do Cliente"
            v-mask="'(##) # ####-####'"
          ></b-form-input>
        </b-form-group>

        <b-form-group label="E-mail do Cliente" v-if="object.contacts[index].type === '2'">
          <input :disabled="!!seeOnly" required type="email" v-model="object.contacts[index].contact" class="form-control" placeholder="E-mail do Cliente">
        </b-form-group>

        <b-form-group label="Telefone do Cliente" v-if="object.contacts[index].type === '3'">
          <b-form-input
            v-model="object.contacts[index].contact"
            required
            :disabled="!!seeOnly"
            placeholder="Telefone do Cliente"
            v-mask="'(##) ####-####'"
          ></b-form-input>
        </b-form-group>
      </b-col>
    </b-row>

    <hr v-if="!seeOnly">
    <b-button v-if="!seeOnly" type="submit" variant="primary">Enviar</b-button>
  </b-form>
</template>

<script>
import { mask } from 'vue-the-mask'

export default {
  name: 'Form',
  props: {
    object: {
      type: Object,
      default: () => {}
    },
    seeOnly: {
      default: () => ''
    }
  },
  methods: {
    remove (position) {
      this.object.contacts = this.object.contacts.filter((item, index) => {
        if (index !== position) {
          return item
        }
      })
    },
    clearField (index) {
      this.object.contacts[index].contact = ''
    },
    addContact () {
      this.object.contacts.push({
        contact: '',
        type: '1'
      })
    },
    onSubmit () {
      this.$emit('onSubmit', this.object)
    }
  },
  directives: { mask }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
