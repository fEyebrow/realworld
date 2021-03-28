<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">Your Settings</h1>

          <form>
            <fieldset>
                <fieldset class="form-group">
                  <input v-model="profile" class="form-control" type="text" placeholder="URL of profile picture">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="name" class="form-control form-control-lg" type="text" placeholder="Your Name">
                </fieldset>
                <fieldset class="form-group">
                  <textarea v-model="bio" class="form-control form-control-lg" rows="8" placeholder="Short bio about you"></textarea>
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="email" class="form-control form-control-lg" type="text" placeholder="Email">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="password" class="form-control form-control-lg" type="password" placeholder="Password">
                </fieldset>
                <button @click.prevent="update" class="btn btn-lg btn-primary pull-xs-right">
                  Update Settings
                </button>
            </fieldset>
          </form>
          <hr />
          <button class="btn btn-outline-danger" @click="onLogout">
            Or click here to logout.
          </button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { updateSetting } from '@/api/user'
import { mapMutations } from 'vuex'
export default {
  middleware: 'authenticated',
  name: 'SettingsIndex',
  data() {
    return {
      bio: '',
      email: '',
      profile: '',
      password: '',
      name: this.$store.state.user.username
    }
  },
  methods: {
    ...mapMutations(['setUser']),
    async update() {
      try {
        const body = {
          user: {
            bio: this.bio,
            email: this.email,
            image: this.profile,
            password: this.password,
            username: this.name
          }
        }
        const { data: { user } } = await updateSetting(body)
        this.$router.push('/profile/' + user.username)
      } catch (error) {
        
      }
    },
    onLogout() {
      this.setUser(null)
      this.$router.replace('/')
    }
  }
}
</script>

<style>

</style>
