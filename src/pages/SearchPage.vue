<template>
  <q-page class="bg-white">
    <div class="q-pt-lg">
      <div class="text-center q-py-md">
        <span>
          Found {{ ResultRows }} search result for : {{ SearchKeyword }}
        </span>
      </div>
      <div class="q-px-md q-gutter-md">
        <q-card
          class="my-card q-px-lg q-py-lg"
          v-for="(res, i) in SearchResult"
          :key="i"
        >
          <q-card-section horizontal>
            <img class="img" :src="res.artworkUrl60" />

            <q-card-section class="q-py-none q-my-none">
              <div class="title">{{ res.artistName }}</div>
              <div class="album q-mb-lg">{{ res.collectionName }}</div>
              <div class="row q-gutter-md">
                <div class="">
                  <q-btn rounded :size="sm" outline color="primary">Play</q-btn>
                </div>
                <div class="float-right">
                  <q-btn flat :size="sm" color="accent">
                    $ {{ res.collectionPrice }}
                  </q-btn>
                </div>
              </div>
            </q-card-section>
          </q-card-section>
        </q-card>
      </div>
    </div>
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
      SearchKeyword: '',
      SearchResult: {},
      ResultRows: 0,
      baseUrl: 'https://itunes.apple.com/search',
    };
  },
  methods: {
    async search() {
      let enc = this.keyword.replace(/\s/g, '+');
      enc = encodeURI(enc);
      let url = `?term=${enc}&limit=25`;
      let result = '';
      console.log(enc);
      api
        .get(url)
        .then((response) => {
          result = response.data.results;
          localStorage.setItem('searchResult', JSON.stringify(result));
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
  beforeMount() {
    let Result = window.localStorage.getItem('SearchResult');
    Result = JSON.parse(Result);
    this.ResultRows = Object.keys(Result).length;
    this.SearchResult = Result;
    this.SearchKeyword = window.localStorage.getItem('SearchKeyword');
  },
};
</script>
