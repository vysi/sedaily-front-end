<template>
  <div class="container">
    <div v-if="loading" class="profile-loading"><spinner :show="loading"/></div>
    <div v-else-if="error" class="bg-danger"> Error: {{ error }}</div>
    <div v-else>

      <profile-details :userData="user" />

      <div class="row">
        <div class="profile-item col-md-8">
          <profile-activities
            :userData="user"
            :activities="activities"
            :activityDays="activityDays" />
        </div>

        <div class="profile-item col-md-4">
          <profile-topics v-if="user" :user="user" />
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import { mapActions, mapState } from 'vuex'
import ProfileDetails from '@/components/profile/ProfileDetails'
import ProfileActivities from './ProfileActivities'
import ProfileTopics from './ProfileTopics'
import Spinner from '@/components/Spinner'

export default {
  name: 'public-profile-view',
  components: {
    ProfileDetails,
    ProfileActivities,
    ProfileTopics,
    Spinner
  },
  data () {
    return {
      loading: false,
      error: null,
      user: null,
      badges: null,
      activities: null,
      activityDays: 0
    }
  },

  mounted () {
    this.fetchData()
  },

  methods: {
    ...mapActions([
      'fetchPublicProfileData',
    ]),

    async fetchData () {
      this.loading = true
      try {
        const response = await this.fetchPublicProfileData({ userId: this.userId })

        if (response.data) {
           this.user = response.data.user || {}
           this.activities = response.data.activities
           this.badges = response.data.badges || []
           this.activityDays = response.data.activityDays
        }
      }
      catch (error) {
        this.error = error.response.data.message
      }
      finally {
        this.loading = false
      }
    }
  },

  computed: {
    ...mapState({
      userId (state) {
        return state.route.params.id
      }
    })
  }
}
</script>
<style lang="stylus" scoped>
.container
  .profile-loading
    text-align center

@media (max-width 750px)
  .profile-item
    margin-bottom 2rem

</style>
