<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-btn type="primary" @click="googleLogin">Login with Google</v-btn>
    </v-col>
  </v-row>
</template>

<script>
import firebase from 'firebase'
export default {
  methods: {
    googleLogin() {
      const provider = new firebase.auth.GoogleAuthProvider()
      this.$fire.auth
        .signInWithPopup(provider)
        .then((result) => {
          const credential = result.credential
          const token = result.accessToken
          const user = result.user
          console.clear()
          console.log(credential, token, user, result)
        })
        .catch((error) => {
          // Handle Errors here.
          const errorCode = error.code
          const errorMessage = error.message
          // The email of the user's account used.
          const email = error.email
          // The firebase.auth.AuthCredential type that was used.
          const credential = error.credential
          // ...
          console.clear()
          console.error(errorCode, errorMessage, email, credential)
        })
    },
  },
}
</script>
