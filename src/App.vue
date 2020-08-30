<template>
  <div id="container">
    <SearchBar v-on:new-input="fetchData" />
    <SearchResults :prop="results" @fetch-definition="fetchDefinition">
      <section id="results">
        <section id="japRes">
          <div class="loader" v-if="searchingJap"></div>
          <h2
            v-if="japRes"
            class="collapse"
            @click="
              (e) =>
                e.target.parentNode.querySelectorAll('article').forEach((x) => {
                  x.style.display == 'none'
                    ? (x.style.display = 'block')
                    : (x.style.display = 'none');
                })
            "
          >
            日本語
          </h2>

          <article
            v-for="(entry, index) in japRes"
            :key="index"
            class="definition"
            v-html="entry.innerHTML"
          ></article>
        </section>

        <section id="engRes">
          <div class="loader" v-if="searchingEng"></div>
          <h2
            v-if="engRes"
            class="collapse"
            @click="
              (e) =>
                e.target.parentNode.querySelectorAll('article').forEach((x) => {
                  x.style.display == 'none'
                    ? (x.style.display = 'block')
                    : (x.style.display = 'none');
                })
            "
          >
            English
          </h2>

          <article
            v-for="(entry, index) in engRes"
            :key="-index - 1"
            class="definition"
            v-html="entry.innerHTML"
          ></article>
        </section>
      </section>
    </SearchResults>
  </div>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import SearchResults from "./components/SearchResults.vue";

import "./assets/css/main.css";

import * as firebase from "firebase/app";
import "firebase/analytics";

const firebaseConfig = {
  apiKey: process.env.VUE_APP_API_KEY,
  authDomain: "adroit-autumn-237308.firebaseapp.com",
  databaseURL: "https://adroit-autumn-237308.firebaseio.com",
  projectId: "adroit-autumn-237308",
  storageBucket: "adroit-autumn-237308.appspot.com",
  messagingSenderId: "411441913058",
  appId: "1:411441913058:web:7ddf2db626f6a036df92bb",
  measurementId: "G-2K44H693HQ",
};

firebase.initializeApp(firebaseConfig);

const parser = new DOMParser();

export default {
  name: "App",
  components: {
    SearchBar,
    SearchResults,
  },

  data: function() {
    return {
      currentWord: null,
      results: null,
      japRes: null,
      engRes: null,
      searchingJap: false,
      searchingEng: false,
    };
  },

  computed: {
    api: () => process.env.VUE_APP_API_KEY,

    cx: () => "partner-pub-8353708690493468:1896422876",
  },

  methods: {
    async fetchData(input) {
      const value = input.value;

      input.style.background = "black";
      input.value = "";

      console.log("fired fetchData", "input", value);

      const uri = `https://www.googleapis.com/customsearch/v1?key=${this.api}&cx=${this.cx}&q=${value}`;
      console.log(uri);
      const response = await fetch(uri);
      const data = await response.json();
      this.results = data.items;
      console.log(data);
    },



    async fetchDefinition(e) {
      Promise.all([
        this.fetchJap(e.link),
        this.fetchEng(e.title),
      ]);
    },

    async fetchJap(url) {
      this.searchingJap = true;
      this.japRes = null;
      console.log("fetching definitionfrom:", url);

      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(url)}`
      );
      const data = await response.json();
      let htmlData = parser
        .parseFromString(data.contents, "text/html")
        .getElementById("mainArea")
        .querySelectorAll("article .description");

      htmlData.forEach((x) => {
        x.querySelectorAll("a").forEach((x) => {
          let y = (document.createElement("span").innerText = x.innerText);
          x.replaceWith(y);
        });
        x.querySelectorAll("img").forEach((x) => {
          x.remove();
        });
      });
      this.japRes = htmlData ? htmlData : "No Results";
      this.searchingJap = false;
      console.log(data, this.japRes);
    },

    async fetchEng(term) {
      this.searchingEng = true;
      this.engRes = null;
      console.log("fethcing english defnitnon", term);
      let match = null;

      try {
        match = term.match(/(.*)\(/i)[1];
      } catch (e) {
        console.log("no match", e);
      }
      term = match ? match : term.replace(/とは\s-\s.*/g, "");
      console.log("fetching for term:", term);
      const uri = await `http://nihongo.monash.edu/cgi-bin/wwwjdic?1MUJ${encodeURIComponent(
        term
      )}`;
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(uri)}`
      );
      const data = await response.json();

      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .querySelectorAll("div[style='clear: both;']");

      htmlData.forEach((x) => {
        x.querySelectorAll("div > input").forEach((x) => {
          x.remove();
        });
        x.querySelectorAll("a").forEach((x) => {
          if (x.innerText == "[Links]") {
            x.remove();
          } else {
            let y = (document.createElement("span").innerText = x.innerText);
            x.replaceWith(y);
          }
        });
      });

      this.engRes = htmlData.length != 0 ? htmlData : [<p>NO Results</p>];
      this.searchingEng = false;

      console.log("parsed value: ", htmlData);
    },
  },
};
</script>
