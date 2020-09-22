<template>
  <div class="user-profile">
    <div class="user-profile__sidebar">
      <div class="user-profile__user-panel">
        <h1 class="user-profile__user-name">@{{ state.user.username }}</h1>
        <div class="user-profile__admin-badge" v-if="state.user.isAdmin">
            Admin
        </div>
        <div class="user-profile__follower-count">
            <strong>Followers</strong> {{state.followers }}
        </div>
        <CreateTweetPanel @add-tweet="addTweet"/>
      </div>
    </div>
    <div class="user-profile__tweets-wrapper">
        <div class="user-profile__tweet" >
            <TweetItem v-for="tweet in state.user.tweets" :key="tweet.id" 
              :username="state.user.username" 
              :tweet="tweet" 
              @favorite="toggleFavorite"/>
        </div>
    </div>
  </div>
</template>

<script>

import TweetItem from '@/components/TweetItem';
import CreateTweetPanel from '@/components/CreateTweetPanel';
import { reactive, computed } from 'vue';
import { useRoute } from 'vue-router';
import { users } from '@/assets/users.js';

export default {
    name: 'UserProfile',
    components: {
      TweetItem,
      CreateTweetPanel
    },
    setup() {
      const route = useRoute();
      const userId = computed(() => route.params.userId);

      const state = reactive({
        followers: 0,
        user: users[userId.value - 1] || users[0]
      });

      function addTweet(tweet) {
        state.user.tweets.unshift({
          id: state.user.tweets.length + 1,
          content: tweet
        });
      }

      return {
        state,
        addTweet,
        userId
      }
    },
  watch: {
    followers(newFollowerCount, oldFollowerCount) {
      if (oldFollowerCount < newFollowerCount) {
        console.log(`${this.user.username} has gained a follower!`);
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.user-profile {
    display: grid;
    grid-template-columns: 1fr 3fr;
    grid-gap: 50px;
    padding: 50px 5%;

    .user-profile__user-panel {
        display: flex;
        flex-direction: column;
        padding: 20px;
        background-color: white;
        border-radius: 5px;
        border: 1px solid #DFE3E8;
        margin-bottom: auto;

        h1 {
            margin: 0;
        }

        .user-profile__admin-badge {
            background: rebeccapurple;
            color: white;
            border-radius: 5px;
            margin-right: auto;
            padding: 0 10px;
            font-weight: bold;
        }

        .user-profile__create-tweet {
          border-top: 1px solid #DFE3E8;
          padding-top: 10px;
          display: flex;
          flex-direction: column;

          &.--exceed {
            color: red;
            border-color: red;

            button {
              background-color: red;
              color: white;
              border: none;
            }
          }
        }
    }

    .user-profile__tweets-wrapper {
        display: grid;
        grid-gap: 10px;
        margin-bottom: auto;
    }

    
}


</style>