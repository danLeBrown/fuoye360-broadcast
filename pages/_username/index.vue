<template>
  <div>
    <PageMetaComponent />
    <div style="border-bottom: 1px solid var(--border-color)">
      <div class="img-banner">
        <img
          :src="viewed_user.banner"
          :alt="`${viewed_user.full_name}-profile-banner`"
          lazy="load"
        />
      </div>
      <v-container class="profile-meta-div">
        <div class="top-meta-div">
          <div class="profile-img">
            <img
              :src="viewed_user.image"
              :alt="`${viewed_user.full_name}-profile-picture`"
              lazy="load"
            />
          </div>
          <div v-if="viewed_user.id === user.id" class="btn-div">
            <button v-ripple type="button" @click="showEditProfile">Edit Profile</button>
          </div>
        </div>
        <div class="bottom-meta-div">
          <h2>
            {{ viewed_user.full_name }}
            <span class="verified-patch"><i class="bi bi-patch-check-fill"></i></span>
          </h2>
          <span
            ><b>@{{ viewed_user.username }}</b></span
          >
          <p class="profile-bio">
            {{ viewed_user.bio }}
          </p>
          <div class="profile-meta grid-row">
            <div>
              <h3 class="bi bi-geo-alt meta-icon"></h3>
              <span>{{ viewed_user.location }}</span>
            </div>
            <div>
              <h3 class="bi bi-link-45deg meta-icon"></h3>
              <a
                :href="viewed_user.url"
                style="color: var(--brand-color)"
                target="_blank"
                >{{ viewed_user.url }}</a
              >
            </div>
            <div>
              <h3 class="bi bi-calendar-event meta-icon"></h3>
              <span>{{ viewed_user.relative_at }}</span>
            </div>
          </div>
          <div class="profile-influence">
            <div>
              <h3>{{ viewed_user.following ? viewed_user.following.length : 0 }}</h3>
              <span>Following</span>
            </div>
            <div>
              <h3>{{ viewed_user.followers ? viewed_user.followers.length : 0 }}</h3>
              <span>Followers</span>
            </div>
          </div>
        </div>
      </v-container>
    </div>
    <BroadcastGridComponent />
  </div>
</template>

<script>
import User from "~/assets/js/api/User";
export default {
  name: "UserIndexPage",
  middleware: ["authenticated"],
  data() {
    return {
      viewed_user: {},
    };
  },
  computed: {
    user() {
      return this.$store.state.User.data;
    },
  },
  mounted() {
    if (this.$route.params.username === this.user.username) {
      this.viewed_user = this.user;
    }
    return this.setupUsernamePage();
  },
  methods: {
    async setupUsernamePage() {
      try {
        const res = await User.findByUsername(this.$route.params.username);
        this.viewed_user = res.data.data.user;
        this.$root.$emit("userProfileRetreived", this.viewed_user);
      } catch (error) {}
    },
    showEditProfile() {
      return this.$root.$emit("showEditProfile");
    },
  },
};
</script>

<style scoped>
.img-banner {
  height: 200px;
}

.img-banner img {
  width: 100%;
  height: inherit;
  object-fit: cover;
}
.profile-meta-div {
  padding-left: 2rem;
  padding-right: 2rem;
}
.top-meta-div {
  display: flex;
  justify-content: space-between;
  margin-top: -5rem;
}

.profile-img img {
  padding: 0.25rem;
  background: var(--white-color);
  width: 125px;
  height: 125px;
  border-radius: 50%;
  object-fit: cover;
}
.top-meta-div .btn-div {
  align-self: flex-end;
}
.top-meta-div button {
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  border: 1px solid var(--brand-color);
  color: var(--brand-color);
  font-weight: 500;
  cursor: pointer;
  align-self: bottom;
}
.top-meta-div button:hover {
  box-shadow: 0 4px 10px 0 var(--green-bg);
}

.bottom-meta-div {
  font-size: 14px;
}
.verified-patch {
  color: var(--brand-color);
}
.profile-bio {
  margin: 1rem 0;
  font-weight: 500;
}
.profile-meta,
.profile-influence {
  display: flex;
  margin-bottom: 0.5rem;
  column-gap: 1rem;
}
.profile-meta div,
.profile-influence div {
  column-gap: 0.5rem;
  display: flex;
  align-items: center;
}

.grid-row {
  display: grid;
  grid-gap: 0.5rem;
}
@media screen and (min-width: 1024px) {
  .grid-row {
    display: flex;
  }
}
</style>
