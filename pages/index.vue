<template>
  <v-container>
    <v-row align="start" no-gutters>
      <v-col :cols="3">
        <v-card class="pa-2" outlined title>
          Hello {{ getName }}
          <nuxt-link :to="'/user/profile?id=' + user.userId"
            >View Profile</nuxt-link
          >
        </v-card>
      </v-col>
      <v-col>
        <v-card class="pa-5" outlined title>
          <div>
            <v-row
              ><v-textarea
                name="input-7-1"
                label="Write your katha.."
                v-model="katha.content"
                :value="katha.content"
                hint="Hint text"
              ></v-textarea
            ></v-row>
            <v-row>
              <v-file-input
                accept="image/*"
                label="Select your image"
                v-model="katha.fileURI"
              ></v-file-input>
            </v-row>
            <v-row><v-btn @click="saveKatha"> Post </v-btn></v-row>
          </div></v-card
        >
        <div>
          <v-card
            v-for="(kathaPost, indx) in kathaPosts"
            :key="kathaPost.userID + indx"
          >
            <b>{{ kathaPost.name }}</b
            ><br />
            <i>{{ kathaPost.createdAt }}</i>
            <p>{{ kathaPost.content }}</p>
            <v-img
              :src="kathaPost.imagePath"
              max-width="200"
              max-height="200"
            ></v-img>
          </v-card>
        </div>
      </v-col>
      <v-col :cols="3">
        <v-card class="pa-2" outlined title>
          <nuxt-link to="login">Go to Login</nuxt-link>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      user: {},
      katha: {
        content: '',
        fileURI: [],
      },
      kathaPosts: [],
    }
  },
  computed: {
    getName() {
      return this.user.displayName ?? 'Stranger'
    },
  },
  async mounted() {
    if (this.$cookies.get('userDetails')) {
      this.user = this.$cookies.get('userDetails')
      console.log(this.user)
      //  TODO: Welcome message
    } else {
      this.$router.push('/login')
    }
    await this.getKathas()
  },
  methods: {
    async saveKatha() {
      try {
        const imagePath = await this.getImagePath()
        const newPost = {
          userID: this.user.userId,
          email: this.user.email,
          name: this.user.displayName,
          createdAt: new Date().to,
          deletedAt: null,
          upvotes: {},
          downvotes: {},
          shares: {},
          imagePath,
          content: this.katha.content,
        }
        //  TODO: Generate uuid for the post
        await this.$fire.firestore
          .collection('posts')
          .doc(newPost.userID + newPost.createdAt)
          .set(newPost)
      } catch (err) {
        console.log(err)
      }
    },
    async getImagePath() {
      const storageRef = await this.$fire?.storage
        .ref('post-images/' + Math.floor(Math.random() * 10000))
        .put(this.katha.fileURI)
      return storageRef.metadata.bucket + '/' + storageRef.metadata.fullPath
    },
    async getKathas() {
      try {
        const snapshot = await this.$fire.firestore.collection('posts').get()
        const storage = await this.$fire.storage.ref()
        await snapshot?.docs.map(async (doc) => {
          const returnData = { ...doc.data() }
          const path = await storage
            .child(returnData.imagePath)
            .getDownloadURL()
          console.log(path)
          this.kathaPosts = [
            ...this.kathaPosts,
            { ...returnData, imagePath: path },
          ]
          return { ...returnData, imagePath: path }
        })
      } catch (err) {
        console.log('Error', err)
      }
    },
  },
}
</script>
