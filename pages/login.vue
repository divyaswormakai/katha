<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <h1>This is login page</h1>
      <v-btn type="primary" @click="googleLogin">Login with Google</v-btn>
    </v-col>
  </v-row>
</template>

<script>
import firebase from 'firebase'

export default {
  name: 'login',
  methods: {
    async googleLogin() {
      try {
        const provider = new firebase.auth.GoogleAuthProvider()
        const auth = await this.$fire.auth.signInWithPopup(provider)
        if (!auth.user) {
          throw new Error('Cannot login with Google')
        }
        const user = auth.user
        console.log('User', user)
        const document = await this.$fire.firestore
          .collection('users')
          .doc(user.uid)
          .get()
        console.log('Document', document)
        if (document && document?.exists) {
          await document.ref.update({
            updated: new Date().toISOString(),
          })
        } else {
          await document.ref.set(
            {
              id: user.uid,
              email: user.email,
              name: user.displayName,
              created: new Date().toISOString(),
              updated: new Date().toISOString(),
            },
            { merge: true }
          )
        }
        // TODO: save user
        this.$cookies.set('userDetails', {
          userId: user.uid,
          displayName: user.displayName,
          email: user.email,
        })
      } catch (err) {
        console.log('Error', err)
      }
    },
  },
}
</script>

<style scoped></style>
