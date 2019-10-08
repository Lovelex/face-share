<template>
  <nav class="navbar">
    <v-toolbar dark flat color="blue darken-3">
      <v-row justify="center">
        <v-toolbar-title class="title text-center">Face Share</v-toolbar-title>
      </v-row>
      <!-- DIALOG START -->
      <div>
        <v-dialog max-width="500px" v-model="dialog">
          <template v-slot:activator="{ on }">
            <v-btn v-on="on" absolute right bottom color="pink" fab>
              <v-icon class="display-1">mdi-plus</v-icon>
            </v-btn>
          </template>
          <v-card>
            <v-card-title class="grey lighten-2 grey--text text--darken-2">
              Adicionar
            </v-card-title>
            <div class="pa-12">
              <!-- FORM START -->
              <v-form ref="form" @submit.prevent="submit">
                <v-text-field :rules="rules" v-model.trim="email" label="E-mail" type="email">

                </v-text-field>
                <v-text-field :rules="rules" v-model.trim="password" label="Senha" type="text">

                </v-text-field>
                <div class="text-right">
                  <v-btn :loading="loading" type="submit" dark color="blue" class="mx-2">Adicionar</v-btn>
                  <v-btn dark color="red" @click="reset">Cancelar</v-btn>
                </div>
              </v-form>
              <!-- FORM END -->
            </div>
          </v-card>
        </v-dialog>
      </div>
      <!-- DIALOG END -->
    </v-toolbar>
    <Dialog />
  </nav>
</template>

<script>
import { db } from '../plugins/firebase.js'
import moment from 'moment'
moment.locale('pt-br')

export default {
  name: "Navbar",
  data() {
    return {
      dialog: false,
      email: '',
      password: '',
      loading: false,
      rules: [
        v => !!v || 'Você deve preencher este campo'
      ]
    }
  },

  methods: {
    submit() {
      if(this.$refs.form.validate()) {
        this.loading = true
        const face = {
          email: this.email,
          password: this.password,
          createdAt: moment(Date.now()).format('L'),
          status: 'Indo'
        }
        db.collection('faces')
          .add(face)
          .then(() => {
            this.reset()
          })
          .catch(err => console.log(err))
          .finally(() => this.loading = false)
   
      }
    },

    reset() {
      this.dialog = false,
      this.email = '',
      this.password = ''
    }
  }
};
</script>

<style>
</style>