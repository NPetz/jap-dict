<template>
  <main>
    <header id="searchArea">
      <SearchBar @search="fetchOptions($event)" />
      <SearchOptions
        :prop="{ options, fetchingOptions, error: optionsError }"
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
      <svg
        width="42.856"
        height="16.544"
        viewBox="0 0 42.856 16.544"
        xmlns="http://www.w3.org/2000/svg"
        id="logotype"
      >
        <g
          stroke-linecap="round"
          fill-rule="evenodd"
          font-size="9pt"
          fill="#333"
          style="fill: #333"
        >
          <path
            d="M 8.69 16.324 L 5.764 12.628 L 4.972 13.486 L 4.972 16.324 L 0 16.324 L 0 0 L 4.972 0 L 4.972 7.788 L 8.426 4.29 L 14.3 4.29 L 9.328 9.504 L 14.696 16.324 L 8.69 16.324 Z M 42.79 11.462 L 34.43 11.462 Q 34.694 12.144 35.31 12.507 A 2.486 2.486 0 0 0 36.027 12.785 Q 36.332 12.854 36.685 12.867 A 4.496 4.496 0 0 0 36.85 12.87 A 5.283 5.283 0 0 0 37.408 12.842 Q 37.681 12.813 37.914 12.754 A 2.686 2.686 0 0 0 38.181 12.672 Q 38.61 12.514 39.102 12.176 A 6.926 6.926 0 0 0 39.358 11.99 L 41.954 14.608 A 5.574 5.574 0 0 1 39.381 16.187 Q 38.495 16.455 37.427 16.522 A 11.672 11.672 0 0 1 36.696 16.544 A 10.055 10.055 0 0 1 34.77 16.368 A 7.688 7.688 0 0 1 32.868 15.741 A 6.449 6.449 0 0 1 31.172 14.566 A 5.774 5.774 0 0 1 30.316 13.508 Q 29.414 12.078 29.414 10.296 A 6.236 6.236 0 0 1 29.753 8.216 A 5.683 5.683 0 0 1 30.305 7.073 Q 31.196 5.654 32.747 4.862 A 7.279 7.279 0 0 1 35.405 4.108 A 8.892 8.892 0 0 1 36.234 4.07 A 7.965 7.965 0 0 1 38.468 4.375 A 7.086 7.086 0 0 1 39.567 4.796 Q 41.074 5.522 41.965 6.93 Q 42.856 8.338 42.856 10.296 Q 42.856 10.406 42.79 11.462 Z M 19.371 16.29 A 8.576 8.576 0 0 0 21.494 16.544 A 9.37 9.37 0 0 0 22.599 16.48 A 7.513 7.513 0 0 0 25.113 15.741 A 7.225 7.225 0 0 0 25.31 15.638 A 6.053 6.053 0 0 0 27.599 13.519 A 5.683 5.683 0 0 0 28.151 12.376 A 6.236 6.236 0 0 0 28.49 10.296 A 7.095 7.095 0 0 0 28.482 9.961 A 5.854 5.854 0 0 0 27.599 7.073 A 5.858 5.858 0 0 0 26.973 6.247 A 6.216 6.216 0 0 0 25.113 4.862 A 7.309 7.309 0 0 0 23.523 4.294 A 8.877 8.877 0 0 0 21.494 4.07 Q 19.492 4.07 17.897 4.862 A 7.227 7.227 0 0 0 17.798 4.912 A 6.079 6.079 0 0 0 15.4 7.073 A 5.655 5.655 0 0 0 14.85 8.191 A 6.159 6.159 0 0 0 14.498 10.296 Q 14.498 12.1 15.4 13.519 A 6.008 6.008 0 0 0 15.997 14.307 A 6.358 6.358 0 0 0 17.897 15.741 A 7.241 7.241 0 0 0 19.371 16.29 Z M 21.494 12.672 A 1.912 1.912 0 0 0 22.242 12.53 A 1.803 1.803 0 0 0 22.902 12.045 A 2.079 2.079 0 0 0 23.33 11.243 Q 23.452 10.824 23.452 10.296 A 3.679 3.679 0 0 0 23.393 9.614 Q 23.272 8.972 22.902 8.558 A 1.787 1.787 0 0 0 21.6 7.944 A 2.313 2.313 0 0 0 21.494 7.942 A 1.947 1.947 0 0 0 20.757 8.077 A 1.799 1.799 0 0 0 20.086 8.558 A 2.018 2.018 0 0 0 19.665 9.328 Q 19.572 9.633 19.546 10 A 4.177 4.177 0 0 0 19.536 10.296 A 3.699 3.699 0 0 0 19.595 10.979 Q 19.716 11.624 20.086 12.045 Q 20.636 12.672 21.494 12.672 Z M 34.342 9.064 L 38.214 9.064 A 2.363 2.363 0 0 0 38.012 8.471 A 1.853 1.853 0 0 0 37.554 7.887 A 1.786 1.786 0 0 0 36.657 7.487 A 2.437 2.437 0 0 0 36.278 7.458 A 2.332 2.332 0 0 0 35.686 7.53 A 1.759 1.759 0 0 0 35.002 7.876 A 1.809 1.809 0 0 0 34.481 8.597 A 2.527 2.527 0 0 0 34.342 9.064 Z"
            vector-effect="non-scaling-stroke"
          />
        </g>
      </svg>
      <a href="https://github.com/NPetz">made w/ 💖 by NPetz</a>
    </div>
  </main>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import SearchOptions from "./components/SearchOptions.vue";

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
    link: [{ rel: "favicon", href: "favicon.svg" }],
    meta: [
      { charset: "utf-8" },
      { name: "viewport", content: "width=device-width, initial-scale=1" },
      { httpEquiv: "X-UA-Compatible", content: "IE=edge,chrome=1" },
      { name: "HandheldFriendly", content: "true" },
    ],
  },
  components: {
    SearchBar,
    SearchOptions,
  },

  data: () => {
    return {
      options: false,
      japRes: false,
      engRes: false,
      fetchingOptions: null,
      optionsError: false,
      searchingJap: false,
      errorJap: false,
      errorEng: false,

      searchingEng: false,
      currentKeyword: null,
      currentSearch: null,
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
        this.options = words ?? null;
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

      console.log("eng raw data", data);

      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .querySelector("pre");

      let htmlRes = htmlData?.innerText.split("\n")[0] ?? null;

      console.log(htmlRes);

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
  bottom: 10px;
  font-size: 0.6rem;
  transform: translateX(50%);
}
#credits a {
  text-decoration: none;
  color: #333;
  white-space: nowrap;
}
#credits svg {
  width: 50px;
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