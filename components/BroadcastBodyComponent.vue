<template>
  <div class="broadcast-body-component">
    <div class="tweet-img-div">
      <nuxt-link
        :to="{
          name: 'username',
          params: { username: d_broadcast.user.username },
        }"
        ><img
          loading="lazy"
          :alt="`${d_broadcast.user.full_name}'s profile picture`"
          class="tweeter-img"
          :src="d_broadcast.user.image"
      /></nuxt-link>
      <div v-if="d_broadcast.thread" class="thread-icon-div">
        <span class="thread-icon"> <i class="bi bi-newspaper"></i></span>
      </div>
    </div>
    <div class="tweet-content">
      <div v-if="d_broadcast.meta">
        <div
          v-if="d_broadcast.meta._info.count > 0 && !d_broadcast.meta.is_thread"
          class="info-display"
        >
          <div v-if="d_broadcast.meta._info.type === 'retweets'" class="tweet-retweet">
            <div v-if="d_broadcast.meta._info.user.id === user.id">
              <b> <i class="bi bi-megaphone-fill icon"></i> You rebroadcasted this </b>
            </div>
            <div v-else-if="d_broadcast.meta._info.count === 1">
              <i class="bi bi-megaphone-fill icon"></i>
              <b> {{ d_broadcast.meta._info.user.full_name }} rebroadcasted this </b>
            </div>
            <div v-else-if="d_broadcast.meta._info.count === 2">
              <i class="bi bi-megaphone-fill icon"></i>
              <b>
                {{ d_broadcast.meta._info.user.full_name }} and 1 other rebrodcasted
                this</b
              >
            </div>
            <div v-else-if="d_broadcast.meta._info.count > 2">
              <i class="bi bi-megaphone-fill icon"></i>
              <b>
                {{ d_broadcast.meta._info.user.full_name }} and
                {{ d_broadcast.meta._info.count }} others rebroadcasted this
              </b>
            </div>
          </div>
          <div v-else-if="d_broadcast.meta._info.type === 'likes'" class="tweet-liked">
            <div v-if="d_broadcast.meta._info.count === 1">
              <i class="bi bi-heart-fill icon"></i>
              <b> {{ d_broadcast.meta._info.user.full_name }} liked this </b>
            </div>
            <div v-else-if="d_broadcast.meta._info.count === 2">
              <i class="bi bi-heart-fill icon"></i>
              <b> {{ d_broadcast.meta._info.user.full_name }} and 1 other liked this </b>
            </div>
            <div v-else-if="d_broadcast.meta._info.count > 2">
              <i class="bi bi-heart-fill icon"></i>
              <b>
                {{ d_broadcast.meta._info.user.full_name }} and
                {{ d_broadcast.meta._info.count }} others liked this
              </b>
            </div>
          </div>
        </div>
      </div>
      <div class="tweet-profile-div">
        <div class="profile-meta">
          <nuxt-link
            :to="{ name: 'username', params: { username: d_broadcast.user.username } }"
            style="text-decoration: none; color: var(--primary-color); display: flex"
          >
            <span class="tweet-profilename">{{ d_broadcast.user.full_name }}</span>
            <div class="tweet-username-div">
              <span class="tweet-small-meta" style="font-weight: 500"
                >@{{ d_broadcast.user.username }}</span
              >
            </div>
          </nuxt-link>
          <div class="tweet-small-meta">
            <i class="bi bi-dot"> </i>
          </div>
          <div class="tweet-time-div">
            <nuxt-link to="/" class="tweet-small-meta">
              {{ d_broadcast.relative_at }}
            </nuxt-link>
          </div>
        </div>
        <button v-ripple class="tweet-options" type="button" @click="showModal('option')">
          <i class="bi bi-three-dots"></i>
        </button>
      </div>
      <!-- eslint-disable-next-line vue/no-v-html -->
      <div class="tweet-body" v-html="d_broadcast.body"></div>
      <div
        v-if="d_broadcast.media != null && d_broadcast.media.length > 0"
        class="broadcast-media"
      >
        <div
          v-for="(img, index) in d_broadcast.media"
          :key="index"
          v-ripple
          class="img-div"
          @click="viewImage(broadcast.media, index)"
        >
          <img loading="lazy" :src="img" :alt="`${index}- broadcast image`" />
        </div>
      </div>
      <BroadcastButtonComponent :broadcast="d_broadcast" class="tweet-btn-component" />
    </div>
  </div>
</template>

<script>
export default {
  name: "BroadcastBodyComponent",
  // eslint-disable-next-line vue/require-prop-types
  props: ["broadcast", "origin"],
  computed: {
    user() {
      return this.$store.state.User.data;
    },
    d_broadcast() {
      return this.broadcast;
    },
  },
  methods: {
    viewImage(src) {
      return this.$root.$emit("viewImage", src);
    },
    showModal(modal) {
      if (this.type === true) {
        return this.$root.$emit("showModal", {
          tweet: this.d_broadcast,
          type: "origin",
          modal,
        });
      }
      return this.$root.$emit("showModal", {
        tweet: this.d_broadcast,
        type: "",
        modal,
      });
    },
  },
};
</script>

<style scoped>
.broadcast-body-component {
  display: flex;
  align-items: flex-start;
  z-index: 1;
}
.tweet-profile-div {
  width: 100%;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0.5rem 0;
  font-size: 0.75rem;
}
.profile-meta {
  display: flex;
  padding: 0;
  margin: 0;
  flex: 11;
  flex-direction: row;
}

.tweet-profilename {
  font-weight: 700;
  display: flex;
  flex: none;
}

.tweet-username-div {
  margin-left: 0.15rem;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.tweet-options {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: none;
  outline: none;
  border: none;
}

.tweet-small-meta {
  color: var(--grey-color);
}
.tweet-time-div {
  margin-left: 0.15rem;
  display: flex;
  flex: none;
}
.tweet-time-div a {
  text-decoration: none;
}
.tweet-img-div {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.tweet-img-div a {
  flex: 1;
}
.tweeter-img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  object-fit: cover;
  background-color: var(--bg-color);
}

.tweet-content {
  margin-left: 0.5rem;
  width: 100%;
  font-size: 0.98rem;
}

.tweet-body a {
  color: var(--brand-color);
}
.tweet-liked i {
  color: var(--red-color);
}

.tweet-retweet i {
  color: var(--brand-color);
}

.info-display {
  font-size: 0.75rem;
}

.broadcast-media {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  justify-content: space-around;
  column-gap: 0.5rem;
  width: 100%;
  padding: 1rem 0;
}

.broadcast-media .img-div {
  width: 100%;
  max-height: 200px;
  border-radius: 0.5rem;
  position: relative;
  cursor: pointer;
  transition: all ease-in-out 300ms;
}

.broadcast-media img {
  width: inherit;
  height: 100%;
  max-height: 200px;
  object-fit: cover;
  border-radius: inherit;
}

.broadcast-media .img-div::before {
  display: block;
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1;
  width: inherit;
  height: 100%;
  border-radius: inherit;
}
.broadcast-media .img-div:hover::before {
  background: linear-gradient(rgba(0, 0, 0, 0.15), rgba(0, 0, 0, 0.5));
}
.broadcast-media .img-div:hover {
  transform: scale(0.9);
}

.thread-icon-div {
  flex: 1;
  display: flex;
  text-align: center;
  width: 100%;
  margin-top: 75%;
}

.thread-icon {
  background: var(--input-color);
  padding: 0.25rem 0.5rem;
  border-radius: 1rem;
  font-size: 0.75rem;
  width: 100%;
}
</style>
