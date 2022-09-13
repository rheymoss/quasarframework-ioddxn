<template>
  <q-page class="column flex-center bg-primary">
    <div class="q-py-lg text-center">
      <img
        alt="logo"
        class="logo"
        src="https://cdn-icons-png.flaticon.com/512/2995/2995101.png"
      />
    </div>
    <q-form class="text-center" @submit="submit">
      <div class="alert-keyword q-py-md">{{ alert }}</div>
      <input
        class="search"
        v-model="keyword"
        placeholder="Artist / Album / Title"
      />
      <br />
      <button class="submit" type="submit" value="Search">Search</button>
    </q-form>
  </q-page>
</template>

<script>
import { useQuasar } from 'quasar';
import { api } from 'boot/axios';

export default {
  data() {
    return {
      $q: useQuasar(),
      keyword: '',
      alert: '',
      result: '',
      baseUrl: 'https://itunes.apple.com/search',
    };
  },
  methods: {
    async search() {
      let enc = this.keyword.replace(/\s/g, '+');
      enc = encodeURI(enc);
      let url = `?term=${enc}`;
      let result = '';
      console.log(enc);
      api
        .get(url)
        .then((response) => {
          result = response.data.results;
          window.localStorage.setItem('SearchKeyword', this.keyword);
          window.localStorage.setItem('SearchResult', JSON.stringify(result));
          location = '/search';
        })
        .catch(() => {
          $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Loading failed',
            icon: 'report_problem',
          });
        });
    },
    submit() {
      if (this.keyword == '') {
        this.alert = 'Please enter your keyword';
      } else {
        this.search();
      }
    },
  },
};
</script>
