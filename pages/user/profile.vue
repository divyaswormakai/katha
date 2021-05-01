<template>
  <v-container>
    <v-row>
      <v-col :cols="4">
        <h2>User Profile</h2>

        <v-row>
          <label for="email"> Email: </label>
          <p id="email">
            <b>{{ userDetails.email }}</b>
          </p>
        </v-row>
        <v-row>
          <label for="name"> Name: </label>
          <p id="name">
            <b>{{ userDetails.name }}</b>
          </p>
        </v-row>
        <v-row>
          <label for="joined"> Join Date: </label>
          <p id="joined">
            <b>{{ getJoinDate }}</b>
          </p>
        </v-row>
        <v-row>
          <v-col>
            <label for="bio">Short Bio: </label>
            <v-textarea
              solo
              name="input-7-4"
              label="Write your short bio"
              v-if="editBioMode"
              v-model="userDetails.bio"
            ></v-textarea>
            <p id="bio" v-else>
              <b>{{ userDetails.bio }}</b>
            </p>
            <v-btn v-if="editBioMode" @click="updateProfileDetails">Save</v-btn>
            <v-btn v-else @click="editBioMode = true">Edit Bio</v-btn>
          </v-col>
        </v-row>
      </v-col>
      <v-col :cols="8">
        <p>This is where the stuff about history and posts will go</p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'profile',
  data() {
    return {
      userDetails: {},
      editBioMode: false,
    }
  },
  mounted() {
    if (!this.$route.query.id) {
      this.$router.push('/')
    }
    this.getProfileDetails(this.$route.query.id)
  },
  computed: {
    getJoinDate() {
      return new Date(this.userDetails.created).toLocaleDateString()
    },
  },
  methods: {
    async getProfileDetails(userId) {
      try {
        const userData = await this.$fire.firestore
          .collection('users')
          .doc(userId)
          .get()

        if (userData.exists) {
          this.userDetails = { ...userData.data() }
          console.log(this.userDetails)
        }
      } catch (err) {
        console.log('Error', err)
      }
    },
    async updateProfileDetails() {
      try {
        await this.$fire.firestore
          .collection('users')
          .doc(this.userDetails.id)
          .update({
            ...this.userDetails,
          })
        this.editBioMode = false
      } catch (err) {
        console.log('Error', err)
      }
    },
  },
}
</script>

<style scoped></style>
