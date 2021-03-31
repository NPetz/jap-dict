<template>
  <main>
    <SearchBar v-on:search="fetchData($event)" />
    <SearchResults :prop="results" @fetch-definition="fetchDefinition" />

    <section id="results">
      <section id="japRes">
        <div class="loader" v-if="searchingJap"></div>
        <h2 v-if="japRes">日本語</h2>
        <article v-if="japRes">
          {{ japRes }}
        </article>
      </section>

      <section id="engRes">
        <div class="loader" v-if="searchingEng"></div>
        <h2 v-if="engRes">English</h2>

        <article v-if="engRes" class="definition" v-html="engRes"></article>
      </section>
    </section>
    <div id="credits">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="14.048748779296886 -17.088763427734378 471.9025024414062 184.17752685546876"
        preserveAspectRatio="xMidYMid"
      >
        <g transform="translate(83.94500428438187, 105.27000045776367)">
          <path
            d="M23.32 1.23L23.32 1.23Q13.99 1.23 9.28-3.30L9.28-3.30L9.28-3.30Q4.58-7.83 4.58-18.57L4.58-18.57L4.58-34.50L0.70-34.50L0.70-48.93L0.70-48.93Q4.05-48.93 5.72-50.64L5.72-50.64L5.72-50.64Q7.39-52.36 7.39-55.35L7.39-55.35L7.39-59.31L26.05-59.31L26.05-48.93L37.93-48.93L37.93-34.50L26.05-34.50L26.05-20.06L26.05-20.06Q26.05-17.16 27.32-15.80L27.32-15.80L27.32-15.80Q28.60-14.43 31.15-14.43L31.15-14.43L31.15-14.43Q34.50-14.43 38.46-15.22L38.46-15.22L38.46-1.41L38.46-1.41Q35.99-0.44 31.81 0.40L31.81 0.40L31.81 0.40Q27.63 1.23 23.32 1.23L23.32 1.23ZM88.70-14.43L92.58-14.43L92.58 0L69.34 0L69.34-5.98L69.34-5.98Q67.41-3.17 63.93-0.97L63.93-0.97L63.93-0.97Q60.46 1.23 55.44 1.23L55.44 1.23L55.44 1.23Q48.84 1.23 45.06-2.29L45.06-2.29L45.06-2.29Q41.27-5.81 41.27-12.23L41.27-12.23L41.27-12.23Q41.27-28.86 69.52-29.30L69.52-29.30L69.52-29.30Q69.26-32.30 67.23-33.40L67.23-33.40L67.23-33.40Q65.21-34.50 60.37-34.50L60.37-34.50L60.37-34.50Q56.41-34.50 51.88-33.66L51.88-33.66L51.88-33.66Q47.34-32.82 43.56-31.42L43.56-31.42L43.56-47.61L43.56-47.61Q48.22-48.75 53.68-49.46L53.68-49.46L53.68-49.46Q59.14-50.16 64.33-50.16L64.33-50.16L64.33-50.16Q77.44-50.16 83.07-45.41L83.07-45.41L83.07-45.41Q88.70-40.66 88.70-30.71L88.70-30.71L88.70-14.43ZM69.34-20.06L69.34-20.59L69.34-20.59Q65.38-20.59 63.23-19.54L63.23-19.54L63.23-19.54Q61.07-18.48 61.07-16.02L61.07-16.02L61.07-16.02Q61.07-14.52 62.00-13.60L62.00-13.60L62.00-13.60Q62.92-12.67 64.59-12.67L64.59-12.67L64.59-12.67Q66.88-12.67 68.11-14.61L68.11-14.61L68.11-14.61Q69.34-16.54 69.34-20.06L69.34-20.06ZM149.95-14.43L153.82-14.43L153.82 0L126.02 0L126.02-14.43L129.71-14.43L129.71-28.86L129.71-28.86Q129.71-31.77 128.48-33.13L128.48-33.13L128.48-33.13Q127.25-34.50 124.78-34.50L124.78-34.50L124.78-34.50Q119.50-34.50 119.50-28.86L119.50-28.86L119.50-14.43L123.20-14.43L123.20 0L95.39 0L95.39-14.43L99.26-14.43L99.26-34.50L95.39-34.50L95.39-48.93L119.50-48.93L119.50-42.06L119.50-42.06Q125.84-50.16 135.34-50.16L135.34-50.16L135.34-50.16Q142.65-50.16 146.30-45.58L146.30-45.58L146.30-45.58Q149.95-41.01 149.95-33L149.95-33L149.95-14.43ZM211.20-14.43L215.07-14.43L215.07 0L187.26 0L187.26-14.43L190.96-14.43L190.96-28.86L190.96-28.86Q190.96-31.77 189.73-33.13L189.73-33.13L189.73-33.13Q188.50-34.50 186.03-34.50L186.03-34.50L186.03-34.50Q180.75-34.50 180.75-28.86L180.75-28.86L180.75-14.43L184.45-14.43L184.45 0L156.64 0L156.64-14.43L160.51-14.43L160.51-34.50L156.64-34.50L156.64-48.93L180.75-48.93L180.75-42.06L180.75-42.06Q187.09-50.16 196.59-50.16L196.59-50.16L196.59-50.16Q203.90-50.16 207.55-45.58L207.55-45.58L207.55-45.58Q211.20-41.01 211.20-33L211.20-33L211.20-14.43ZM244.11 1.23L244.11 1.23Q231.62 1.23 224.58-5.32L224.58-5.32L224.58-5.32Q217.54-11.88 217.54-24.46L217.54-24.46L217.54-24.46Q217.54-37.05 224.58-43.60L224.58-43.60L224.58-43.60Q231.62-50.16 244.11-50.16L244.11-50.16L244.11-50.16Q256.78-50.16 263.74-43.52L263.74-43.52L263.74-43.52Q270.69-36.87 270.69-24.46L270.69-24.46L270.69-24.46Q270.69-11.88 263.65-5.32L263.65-5.32L263.65-5.32Q256.61 1.23 244.11 1.23L244.11 1.23ZM244.11-14.43L244.11-14.43Q246.66-14.43 247.94-15.80L247.94-15.80L247.94-15.80Q249.22-17.16 249.22-20.06L249.22-20.06L249.22-28.86L249.22-28.86Q249.22-31.77 247.94-33.13L247.94-33.13L247.94-33.13Q246.66-34.50 244.11-34.50L244.11-34.50L244.11-34.50Q241.56-34.50 240.28-33.13L240.28-33.13L240.28-33.13Q239.01-31.77 239.01-28.86L239.01-28.86L239.01-20.06L239.01-20.06Q239.01-17.16 240.28-15.80L240.28-15.80L240.28-15.80Q241.56-14.43 244.11-14.43L244.11-14.43ZM327.54-14.43L331.41-14.43L331.41 0L307.30 0L307.30-6.86L307.30-6.86Q300.96 1.23 291.46 1.23L291.46 1.23L291.46 1.23Q284.15 1.23 280.50-3.34L280.50-3.34L280.50-3.34Q276.85-7.92 276.85-15.93L276.85-15.93L276.85-34.50L272.98-34.50L272.98-48.93L297.09-48.93L297.09-20.06L297.09-20.06Q297.09-17.16 298.36-15.80L298.36-15.80L298.36-15.80Q299.64-14.43 302.19-14.43L302.19-14.43L302.19-14.43Q304.74-14.43 306.02-15.80L306.02-15.80L306.02-15.80Q307.30-17.16 307.30-20.06L307.30-20.06L307.30-34.50L302.54-34.50L302.54-48.93L327.54-48.93L327.54-14.43Z"
            fill="#333"
          ></path>
        </g>
      </svg>
      <a href="https://github.com/NPetz">made w/ 💖 by NPetz</a>
    </div>
  </main>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import SearchResults from "./components/SearchResults.vue";

import * as firebase from "firebase/app";
import "firebase/analytics";

const firebaseConfig = {
  apiKey: "AIzaSyCrYzr1CQ1RYaxeGf5Vu3FLW3WZxlwg-0E",
  authDomain: "adroit-autumn-237308.firebaseapp.com",
  databaseURL: "https://adroit-autumn-237308.firebaseio.com",
  projectId: "adroit-autumn-237308",
  storageBucket: "adroit-autumn-237308.appspot.com",
  messagingSenderId: "411441913058",
  appId: "1:411441913058:web:7ddf2db626f6a036df92bb",
  measurementId: "G-2K44H693HQ",
};

firebase.initializeApp(firebaseConfig);

console.log(process.env);

const parser = new DOMParser();

export default {
  name: "App",
  components: {
    SearchBar,
    SearchResults,
  },

  data: function () {
    return {
      results: false,
      japRes: false,
      engRes: false,
      searchingJap: false,
      searchingEng: false,
      currentKeyword: null,
      currentSearch: null,
    };
  },

  computed: {
    api: () => "AIzaSyCrYzr1CQ1RYaxeGf5Vu3FLW3WZxlwg-0E",

    cx: () => "partner-pub-8353708690493468:1896422876",
  },

  methods: {
    async fetchData(input) {
      const url = encodeURIComponent(
        ` https://sakura-paris.org/dict/?api=1&q=${input}&dict=広辞苑&max=10`
      );
      const uri = `https://api.allorigins.win/get?url=${url}`;
      if (input.length > 0 && input !== this.currentKeyword) {
        this.currentKeyword = input;
        const response = await fetch(uri);
        const data = await response.json();
        const status = data.status.http_code;
        console.log(data);
        if (status === 200) {
          const words = JSON.parse(data.contents).words;
          this.results = words;
        } else {
          console.log("bad request");
        }
      } else {
        console.log("search term empty or same as before");
      }
    },

    async fetchDefinition(e) {
      console.log(e);
      this.japRes = e.text.replace(/\[([^\]]+)\]/g, "");
      this.fetchEng(e.heading.match(/【(.*)】/)[1]);

      // Promise.all([this.fetchJap(e.link), this.fetchEng(e.title)]);
    },

    async fetchJap(url) {
      this.searchingJap = true;
      this.japRes = null;

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
    },

    async fetchEng(term) {
      this.searchingEng = true;
      this.engRes = null;

      const uri = `http://nihongo.monash.edu/cgi-bin/wwwjdic?1MUJ${encodeURIComponent(
        term
      )}`;
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(uri)}`
      );
      const data = await response.json();

      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .querySelectorAll("div[style='clear: both;']");

      let htmlRes = htmlData[0] ?? null;

      if (htmlRes) {
        htmlRes.querySelectorAll("div > input").forEach((x) => {
          x.remove();
        });
        htmlRes.querySelectorAll("a").forEach((x) => {
          if (x.innerText == "[Links]") {
            x.remove();
          } else {
            let y = (document.createElement("span").innerText = x.innerText);
            x.replaceWith(y);
          }
        });

        this.engRes = htmlRes.innerHTML;
      } else {
        this.engRes = "No Results";
      }

      this.searchingEng = false;
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat&family=Noto+Sans+JP&family=Noto+Serif+JP&family=Roboto+Slab&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
body {
  background-color: #eee;
  min-height: 100vh;
  max-width: 100vw;
  font-family: "Montserrat", "Noto Serif JP";
  padding: 1rem;
  overflow: hidden;
}

/* credits */

#credits {
  position: absolute;
  left: 20px;
  bottom: 20px;
  width: 100px;
  font-size: 9px;
}
#credits a {
  text-decoration: none;
  color: #333;
}

/* loader */

.loader {
  font-size: 10px;
  margin: 50px auto;
  text-indent: -9999em;
  width: 11em;
  height: 11em;
  border-radius: 50%;
  background: #333333;
  background: -moz-linear-gradient(left, #333333 10%, rgba(51, 51, 51, 0) 42%);
  background: -webkit-linear-gradient(
    left,
    #333333 10%,
    rgba(51, 51, 51, 0) 42%
  );
  background: -o-linear-gradient(left, #333333 10%, rgba(51, 51, 51, 0) 42%);
  background: -ms-linear-gradient(left, #333333 10%, rgba(51, 51, 51, 0) 42%);
  background: linear-gradient(to right, #333333 10%, rgba(51, 51, 51, 0) 42%);
  position: relative;
  -webkit-animation: load3 1.4s infinite linear;
  animation: load3 1.4s infinite linear;
  -webkit-transform: translateZ(0);
  -ms-transform: translateZ(0);
  transform: translateZ(0);
}
.loader:before {
  width: 50%;
  height: 50%;
  background: #333333;
  border-radius: 100% 0 0 0;
  position: absolute;
  top: 0;
  left: 0;
  content: "";
}
.loader:after {
  background: #eeeeee;
  width: 75%;
  height: 75%;
  border-radius: 50%;
  content: "";
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
@-webkit-keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
</style>