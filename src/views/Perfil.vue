<template>
  <v-container class="pa-8">
    <h1 class="h1">Perfil</h1>
    <v-form>
      <v-container>
        <v-text-field label="Nome" v-model="nome"></v-text-field>
        <v-text-field label="Sobrenome" v-model="sobrenome"></v-text-field>
        <v-btn color="lime darken-1" @click="salvarPerfil">Salvar</v-btn>
      </v-container>
    </v-form>
    <v-snackbar color="green" v-model="perfilSalvo" sucess multline timeout="2000"
      >Salvo com sucesso!</v-snackbar
    >
  </v-container>
</template>

<script>
import * as fb from "@/plugins/firebase";
export default {
  data() {
    return {
      nome: "",
      sobrenome: "",
      uid: "",
      temPerfil: false,
      perfilSalvo: false,
    };
  },
  async mounted() {
    this.uid = fb.auth.currentUser.uid;
    const userProfile = await fb.profileCollection
      .where("uid", "==", this.uid)
      .get();
    if (userProfile.docs.length > 0) {
      this.temPerfil = true
      const perfil = userProfile.docs[0]
      this.profileId = perfil.id
      this.nome = perfil.data().nome
      this.sobrenome = perfil.data().sobrenome
    }
  },
  methods:{
    async salvarPerfil(){
      this.perfilSalvo = true
      if (this.temPerfil){
        await fb.profileCollection.doc(this.profileId).update({
          nome: this.nome,
          sobrenome: this.sobrenome
        })
      } else{
        await fb.profileCollection.add({
          uid:this.uid,
          nome: this.nome,
          sobrenome: this.sobrenome,
        })
      }
    }
  }
};
</script>

<style></style>
