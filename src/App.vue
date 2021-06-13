<template>
  <main>
    <header id="searchArea">
      <SearchBar @search="fetchOptions($event)" />
      <OptionsBar
        :options="options"
        :fetching="fetchingOptions"
        :error="optionsError"
        @fetch-definition="fetchDefinition"
      />
    </header>

    <section id="results" v-if="engRes || japRes">
      <section id="japRes">
        <div class="loader" v-if="searchingJap"></div>
        <article class="resultWrapper" v-if="japRes">
          <h2 class="languageLabel">日本語</h2>
          <div class="definition">
            {{ japRes.def }}
            <ul
              id="links-list"
              @mouseenter="scrollEnter"
              @mouseleave="scrollLeave"
              @mousemove="
                (e) => {
                  if (!throttling) {
                    mousePosition = e.clientX;
                  } else {
                    throttling = true;
                    setTimeout(() => (throttling = false), 300);
                  }
                }
              "
            >
              <li
                class="link"
                v-for="(entry, index) in japRes.links"
                :key="index"
                @click="fetchOptions(entry.match(/【(.*)】/, '')[1])"
              >
                {{ entry }}
              </li>
            </ul>
          </div>
        </article>
      </section>

      <section id="engRes">
        <article v-if="engRes || searchingEng" class="resultWrapper">
          <h2 class="languageLabel">English</h2>
          <div class="loader" v-if="searchingEng"></div>
          <div class="definition" v-if="engRes" v-html="engRes"></div>
        </article>
      </section>
    </section>
    <div id="credits">
      <svg viewBox="0 0 92.9 95.5">
        <g
          id="SvgjsG1000"
          stroke-linecap="round"
          fill-rule="evenodd"
          font-size="9pt"
          stroke="#333"
          fill="#333"
          stroke-width="0.25mm"
          style="stroke: #333; stroke-width: 0"
        >
          <path
            d="M 92.9 19.3 L 92.9 7.1 L 55.8 7.1 L 55.8 0 L 41.1 0 L 41.1 7.1 L 4.2 7.1 L 4.2 19.3 L 41.1 19.3 L 41.1 23.3 L 10.8 23.3 L 10.8 35.4 L 88.3 35.4 L 88.3 23.3 L 55.8 23.3 L 55.8 19.3 L 92.9 19.3 Z M 87.1 39.6 L 12.1 39.6 L 12.1 51.6 C 12.1 59.76 11.447 70.531 5.343 79.589 A 32.494 32.494 0 0 1 0 85.7 C 2.775 87.311 7.953 91.727 10.587 94.572 A 19.843 19.843 0 0 1 11.4 95.5 C 18.5 89 22.4 80.1 24.4 71.2 L 72.5 71.2 L 72.5 76.7 L 87.1 76.7 L 87.1 39.6 Z M 72.5 59.1 L 55.6 59.1 L 55.6 51.3 L 72.5 51.3 L 72.5 59.1 Z M 41.5 51.3 L 41.5 59.1 L 26.2 59.1 A 103.213 103.213 0 0 0 26.454 54.547 A 84.133 84.133 0 0 0 26.5 51.8 L 26.5 51.3 L 41.5 51.3 Z"
            vector-effect="non-scaling-stroke"
          ></path>
        </g>
      </svg>

      <a href="https://github.com/NPetz">made w/ 💖 by NPetz</a>
    </div>
  </main>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import OptionsBar from "./components/OptionsBar/OptionsBar.vue";

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

const parser = new DOMParser();

export default {
  name: "Koe",
  metaInfo: {
    title: "Koe",
  },
  components: {
    SearchBar,
    OptionsBar,
  },

  data: () => {
    return {
      options: null,
      japRes: "",
      engRes: "",
      fetchingOptions: false,
      optionsError: false,
      errorJap: false,
      errorEng: false,
      searchingJap: false,
      searchingEng: false,
      currentKeyword: "",
      currentSearch: "",
      scrollInterval: null,
      mousePosition: null,
      throttling: false,
    };
  },

  methods: {
    async fetchOptions(input) {
      // case search is same as current and there are no errors
      if (input === this.currentKeyword && !this.optionsError) {
        // highligth current search
        return;
      }
      const japApiUrl = encodeURIComponent(
        ` https://sakura-paris.org/dict/?api=1&q=${input}&dict=広辞苑&max=10`
      );
      const proxyUrl = `https://api.allorigins.win/get?url=${japApiUrl}`;

      this.fetchingOptions = true;
      this.options = "";
      this.currentKeyword = input;
      try {
        const response = await fetch(proxyUrl);
        const data = await response.json();
        this.fetchingOptions = false;
        this.optionsError = false;
        const words = JSON.parse(data.contents).words;
        this.options = words;
      } catch (error) {
        this.optionsError = true;
        // TODO if not 200 display try again in search results
        console.log(error);
      }
    },

    fetchDefinition(e) {
      if (e !== this.currentSearch) {
        this.currentSearch = e;

        this.japRes = this.parseJap(e.text);
        this.fetchEng(e.heading.match(/【(.*)】/)[1]);
      } else {
        // TODO if same search shake hits and highlihg tresults
        console.log("same search");
      }
    },

    parseJap(text) {
      // TODO better parsing
      // remove parts in suqre brackets
      text = text.replace(/\[([^\]]+)\]/g, "");
      // split at ⇒
      let [def, ...links] = text.split(/[⇒→]/);

      return { def, links };
    },

    async fetchEng(term) {
      this.searchingEng = true;
      this.engRes = null;

      const uri = `https://www.edrdg.org/cgi-bin/wwwjdic/wwwjdic?1ZUJ${encodeURIComponent(
        term
      )}`;
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(uri)}`
      );
      const data = await response.json();

      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .querySelector("pre");

      let htmlRes = htmlData?.innerText.split("\n")[0] ?? null;

      htmlRes = htmlRes
        .replace(/\//, " ")
        .replace(/\/$/, "")
        .replace(/(?<=\w)(\/)(?=\w)/g, ", ")
        .replace(/\/\(P\)/, "")
        .replace(/]/, "$&\n")
        .replace(/\/(\()/g, "\n$1");

      if (htmlRes) {
        this.searchingEng = false;
        this.engRes = htmlRes;
      } else {
        this.engRes = "No Results";
      }
    },
    scrollEnter(e) {
      let target = e.currentTarget;
      let width = e.currentTarget.offsetWidth;
      let { left: leftCoord } = target.getBoundingClientRect();
      this.scrollInterval = setInterval(() => {
        if (this.mousePosition < leftCoord + width * 0.15) {
          target.scrollLeft -= 25;
        } else if (this.mousePosition > leftCoord + width - width * 0.15) {
          target.scrollLeft += 25;
        }
      }, 50);
    },
    scrollLeave() {
      clearInterval(this.scrollInterval);
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
html,
body {
  height: 100vh;
  overflow: hidden;
  position: relative;
}
body {
  background-color: #eee;
  font-family: "Montserrat", "Noto Serif JP";
  display: flex;
  flex-direction: column;
}
main {
  display: flex;
  flex-direction: column;
  margin-bottom: 50px;
  flex-grow: 1;
  height: 100vh;
  overflow: hidden;
}
/* search Area */

#searchArea {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 1rem;
}

/* results */

#results {
  padding: 1rem;
  display: flex;
  flex-grow: 1;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  overflow-y: auto;
  overflow-x: hidden;
  gap: 1rem;
}
#results::-webkit-scrollbar {
  height: 10px;
  background-color: #eee;
  display: none;
}
#japRes,
#engRes {
  display: flex;
  flex-grow: 1;
  flex-direction: column;
  justify-content: center;
  max-width: 100%;
}

.resultWrapper {
  border: 1px solid #333;
  background-color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.languageLabel {
  display: flex;
  width: 100%;
  align-items: center;
  background-color: #333;
  color: #eee;
  padding: 0 1rem;
  font-size: 1.2rem;
  height: 3rem;
}

.definition {
  word-break: break-word;
  white-space: pre-line;
  font-size: 1.3em;
  padding: 3rem;
  width: 100%;
  max-width: 600px;
}
/* japRes */

#links-list {
  display: flex;
  width: 100%;
  overflow-x: auto;
  list-style: none;
  font-size: 0.8em;
  margin-top: 1em;
  gap: 1rem;
  background: linear-gradient(
    180deg,
    transparent calc(50% - 1px),
    #333 calc(50%),
    transparent calc(50% + 1px)
  );
}
#links-list::-webkit-scrollbar {
  height: 10px;
  background-color: #eee;
  display: none;
}
.link {
  background-color: #333;
  border: 1px solid #333;
  color: #eee;
  white-space: nowrap;
  padding: 0.1rem 0.5rem;
  transition: all 0.2s;
  cursor: pointer;
  user-select: none;
  margin: 0 auto;
}
.link:hover {
  background-color: #fff;
  color: #333;
  border-color: #333;
}

/* credits */

#credits {
  position: absolute;
  right: 50%;
  bottom: 5px;
  font-size: 0.6rem;
  transform: translateX(50%);
}
#credits a {
  text-decoration: none;
  color: #333;
  white-space: nowrap;
}
#credits svg {
  width: 25px;
  height: auto;
  display: block;
  margin: 0 auto;
}
/* loader */

.loader {
  font-size: 10px;
  margin: 3rem;
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
  background: #fff;
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