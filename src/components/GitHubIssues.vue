<template>
  <div class="container">
    <h1>Vue.js + Github</h1>
    <p class="lead">
      Página que lista issues de um repositório do Github, usando Vue.js.
    </p>
    <div class="row">
      <div class="col">
        <div class="form-group">
          <input type="text" v-model="username" class="form-control"
                 placeholder="github username">
        </div>
      </div>
      <div class="col">
        <div class="form-group">
          <input type="text" v-model="repository" class="form-control"
                 placeholder="github repositorio">
        </div>
      </div>

      <div class="col-3">
        <div class="form-group">
          <button class="btn btn-success"
            @click.prevent.stop="getIssues()">GO</button>
          <button class="btn btn-danger"
            @click.prevent.stop="reset()">LIMPAR</button>
        </div>
      </div>
    </div>

    <br><hr><br>

    <table class="table table-sm table-bordered">
      <thead>
      <tr>
        <th width="100">Número</th>
        <th>Título</th>
      </tr>
      </thead>

      <tbody>
        <tr v-if="loader.getIssues">
          <td class="text-center" colspan="2">
            <img src="/static/load2.gif" alt="">
          </td>
        </tr>
          <tr v-if="issues.length > 0 && !loader.getIssues"
              v-for="issue in issues"
              :key="issue.number">
            <td>
              <router-link :to="{
                name: 'GitHubIssue',
                params: {
                  name: username,
                  repo: repository,
                  issue: issue.number,
                  }
              }">
                {{ issue.number }}
              </router-link>
            </td>
            <td>{{ issue.title }}</td>
          </tr>
        <tr v-if="issues.length <= 0 && !loader.getIssues">
          <td class="text-center" colspan="2">Nenhuma issue encontrada!</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'GitHubIssues',
  data() {
    return {
      username: '',
      repository: '',
      issues: [],
      loader: {
        getIssues: false,
      },
    };
  },
  created() {
    this.getLocalData();
  },
  methods: {
    reset() {
      this.username = '';
      this.repository = '';
    },
    getIssues() {
      if (this.username && this.repository) {
        localStorage.setItem('githubissues', JSON.stringify({ username: this.username, repository: this.repository }));
        this.loader.getIssues = true;
        const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
        axios.get(url)
          .then((response) => {
            this.issues = response.data;
          })
          .finally(() => {
            this.loader.getIssues = false;
          });
      }
    },
    getLocalData() {
      const dados = JSON.parse(localStorage.getItem('githubissues'));
      if (dados.username && dados.repository) {
        this.username = dados.username;
        this.repository = dados.repository;

        this.getIssues();
      }
    },
  },
};
</script>
