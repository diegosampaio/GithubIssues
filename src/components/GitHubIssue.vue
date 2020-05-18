<template>
  <div>
    <div v-if="loader.getIssue" class="text-center">
      <br /><br /><br /><br />
      <img src="/static/load2.gif" alt="">
    </div>
    <div class="container" v-if="!loader.getIssue && issue.number">
      <h1>Issue #{{ issue.number }}</h1>
      <h2>{{ issue.title }}</h2>
      <p>{{ issue.body }}</p>

      <router-link :to="{ name: 'GitHubIssues'}" class="btn btn-secondary">
        Voltar
      </router-link>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'GitHubIssue',
  data() {
    return {
      issue: {},
      loader: {
        getIssue: false,
      },
    };
  },
  created() {
    this.getIssue();
  },
  methods: {
    getIssue() {
      this.loader.getIssue = true;
      const url = `https://api.github.com/repos/${this.$route.params.name}/${this.$route.params.repo}/issues/${this.$route.params.issue}`;
      axios.get(url).then((response) => {
        this.issue = response.data;
      })
        .finally(() => {
          this.loader.getIssue = false;
        });
    },
  },
};
</script>
