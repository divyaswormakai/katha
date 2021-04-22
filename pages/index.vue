<template>
  <v-row justify="center" align="center">
    This is Home Page.
    <nuxt-link to="login">Go to login</nuxt-link>
    <!--    TODO: Post something along with photo-->
  </v-row>
</template>

<script>
export default {
  mounted() {
    if (this.$cookies.get('userDetails')) {
      console.log(this.$cookies.get('userDetails').displayName)
      //  TODO: Welcome message
    } else {
      this.$router.push('/login')
    }
  },
}
</script>
