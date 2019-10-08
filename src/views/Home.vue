<template>
  <div class="home mt-12">
    <v-container>
      <v-card class="pa-3 indigo lighten-5">
        <v-card-title class="grey--text text--darken-2">Contas</v-card-title>

        <v-card class="my-3" v-for="(face, index) in faces" :key="face.id">
          <v-row class="grey--text text--darken-2 mx-3" align="center">
            <v-col cols="12" sm="6" lg="3">
              <div>
                <v-icon left>mdi-email</v-icon>
                <span >E-mail:</span>
                <span class="mx-1">{{face.email}}</span>
              </div>
            </v-col>
            <v-col cols="12" sm="6" lg="2">
              <div>
                <v-icon left>mdi-security</v-icon>
                <span>Senha:</span>
                <span class="mx-1">{{face.password}}</span>
              </div>
            </v-col>
            <v-col cols="12" sm="6" lg="3">
              <div>
                <v-icon left>mdi-clock-outline</v-icon>
                <span>Criado:</span>
                <span class="mx-1">{{face.createdAt}}</span>
              </div>
            </v-col>
            <v-col cols="12" sm="6" lg="2">
              <div>
                <span>Status: </span>
                <span>
                  <v-chip  dark :color="statusColor(face.status)">
                    <v-icon left class="headline">{{statusIcon(face.status)}}</v-icon>
                    <span>{{face.status}}</span>
                  </v-chip>
                </span>
              </div>
            </v-col>
            <v-col cols="12" sm="6" lg="2">
              <!-- DIALOG START -->
              <div>
                <v-dialog max-width="500px" v-model="dialog">
                  <template v-slot:activator="{on}">
                    <v-btn @click="pushFace(index)" v-on="on" small fab class="mx-3 orange">
                      <v-icon class="white--text">mdi-pencil</v-icon>
                    </v-btn>
                  </template>
                  <v-card>
                    <v-card-title class="grey lighten-2 grey--text text--darken-2">Editar</v-card-title>
                    <div class="pa-12">
                      <!-- FORM START -->
                      <v-form ref="form" @submit.prevent="edit(pushedFace.id ,index)">
                        <v-text-field
                          :rules="rules"
                          v-model.trim="pushedFace.email"
                          label="E-mail"
                          type="email"
                        ></v-text-field>
                        <v-text-field
                          :rules="rules"
                          v-model.trim="pushedFace.password"
                          label="Senha"
                          type="text"
                        ></v-text-field>
                        <v-select v-model="pushedFace.status" :items="items" label="Standard"></v-select>
                        <div class="text-right">
                          <v-btn
                            :loading="loading"
                            type="submit"
                            dark
                            color="blue"
                            class="mx-2"
                          >Salvar</v-btn>
                          <v-btn dark color="red" @click="dialog = false">Cancelar</v-btn>
                        </div>
                      </v-form>
                      <!-- FORM END -->
                    </div>
                  </v-card>
                </v-dialog>
                <!-- DIALOG END -->
                <v-btn @click="del(face.id, index)" small fab class="red">
                  <v-icon class="white--text">mdi-delete</v-icon>
                </v-btn>
              </div>
            </v-col>
          </v-row>
        </v-card>
      </v-card>
    </v-container>
  </div>
</template>

<script>
import { db } from "../plugins/firebase.js";

export default {
  name: "Home",
  data() {
    return {
      faces: [],
      pushedFace: {},
      dialog: false,
      loading: false,
      items: ["Indo", "Ok", "Deletado"],
      rules: [v => !!v || "Você deve preencher este campo"],
      
    };
  },

  methods: {
    edit(id, index) {
      this.loading = true;

      db.collection("faces")
        .doc(id)
        .update(this.pushedFace)
        .then(() => {
          this.dialog = false
          this.reload()
        })
        .catch(err => console.log(err))
        .finally(() => (this.loading = false));
    },

    pushFace(index) {
      this.pushedFace = this.faces[index];
    },

    reload() {
      db.collection("faces")
        .get()
        .then(snap => {
          this.faces = [];
          snap.docs.forEach(doc => {
            this.faces.push({ ...doc.data(), id: doc.id });
          });
        });
    },

    del(id, index) {
      if(confirm('Quer mesmo deletar esse E-mail?')) {
        db.collection('faces')
        .doc(id)
        .delete()
        .then(() => this.reload())
      }
    },

    statusColor(status) {
      if(status === 'Indo') return 'orange'
      if(status === 'Ok') return 'green'
      if(status === 'Deletado') return 'red'
    },
    statusIcon(status) {
      if(status === 'Indo') return 'mdi-alert-circle-outline'
      if(status === 'Ok') return 'mdi-checkbox-marked-circle'
      if(status === 'Deletado') return 'mdi-close-circle-outline'
    }
  },

  created() {
    const facesRef = db.collection("faces");

    facesRef.onSnapshot(res => {
      const changes = res.docChanges();
      changes.forEach(change => {
        if (change.type === "added") {
          this.faces.push({ ...change.doc.data(), id: change.doc.id });
        }
      });
    });
  },

};
</script>

<style>
</style>