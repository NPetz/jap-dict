<template>
  <main>
    <header id="searchArea">
      <SearchBar @search="fetchOptions" />
      <OptionsBar
        :options="options"
        :fetching="fetchingOptions"
        :error="optionsError"
        @fetch-definition="fetchDefinition"
      />
    </header>

    <ResultsSection
      :engRes="engRes"
      :japRes="japRes"
      :fetchingEng="fetchingEng"
      :errorEng="errorEng"
      @search="fetchOptions"
    />

    <div id="credits">
      <a id="github" href="https://github.com/NPetz"
        ><svg viewBox="0 0 128 128">
          <g fill="#333">
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M64 5.103c-33.347 0-60.388 27.035-60.388 60.388 0 26.682 17.303 49.317 41.297 57.303 3.017.56 4.125-1.31 4.125-2.905 0-1.44-.056-6.197-.082-11.243-16.8 3.653-20.345-7.125-20.345-7.125-2.747-6.98-6.705-8.836-6.705-8.836-5.48-3.748.413-3.67.413-3.67 6.063.425 9.257 6.223 9.257 6.223 5.386 9.23 14.127 6.562 17.573 5.02.542-3.903 2.107-6.568 3.834-8.076-13.413-1.525-27.514-6.704-27.514-29.843 0-6.593 2.36-11.98 6.223-16.21-.628-1.52-2.695-7.662.584-15.98 0 0 5.07-1.623 16.61 6.19C53.7 35 58.867 34.327 64 34.304c5.13.023 10.3.694 15.127 2.033 11.526-7.813 16.59-6.19 16.59-6.19 3.287 8.317 1.22 14.46.593 15.98 3.872 4.23 6.215 9.617 6.215 16.21 0 23.194-14.127 28.3-27.574 29.796 2.167 1.874 4.097 5.55 4.097 11.183 0 8.08-.07 14.583-.07 16.572 0 1.607 1.088 3.49 4.148 2.897 23.98-7.994 41.263-30.622 41.263-57.294C124.388 32.14 97.35 5.104 64 5.104z"
            ></path>
            <path
              d="M26.484 91.806c-.133.3-.605.39-1.035.185-.44-.196-.685-.605-.543-.906.13-.31.603-.395 1.04-.188.44.197.69.61.537.91zm-.743-.55M28.93 94.535c-.287.267-.85.143-1.232-.28-.396-.42-.47-.983-.177-1.254.298-.266.844-.14 1.24.28.394.426.472.984.17 1.255zm-.575-.618M31.312 98.012c-.37.258-.976.017-1.35-.52-.37-.538-.37-1.183.01-1.44.373-.258.97-.025 1.35.507.368.545.368 1.19-.01 1.452zm0 0M34.573 101.373c-.33.365-1.036.267-1.552-.23-.527-.487-.674-1.18-.343-1.544.336-.366 1.045-.264 1.564.23.527.486.686 1.18.333 1.543zm0 0M39.073 103.324c-.147.473-.825.688-1.51.486-.683-.207-1.13-.76-.99-1.238.14-.477.823-.7 1.512-.485.683.206 1.13.756.988 1.237zm0 0M44.016 103.685c.017.498-.563.91-1.28.92-.723.017-1.308-.387-1.315-.877 0-.503.568-.91 1.29-.924.717-.013 1.306.387 1.306.88zm0 0M48.614 102.903c.086.485-.413.984-1.126 1.117-.7.13-1.35-.172-1.44-.653-.086-.498.422-.997 1.122-1.126.714-.123 1.354.17 1.444.663zm0 0"
            ></path>
          </g></svg
      ></a>
      <svg id="logo" viewBox="0 0 92.9 95.5">
        <g
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
    </div>
  </main>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import OptionsBar from "./components/OptionsBar/OptionsBar.vue";
import ResultsSection from "./components/ResultsSection.vue";

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
  components: {
    SearchBar,
    OptionsBar,
    ResultsSection,
  },
  beforeMount() {
    document.querySelector("title").textContent = "Koe";
  },

  data: () => {
    return {
      options: null,
      japRes: "",
      engRes: "",
      fetchingOptions: false,
      optionsError: false,
      errorEng: false,
      fetchingEng: false,
      currentKeyword: "",
      currentSearch: "",
    };
  },

  methods: {
    async fetchOptions(input) {
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
        let words = JSON.parse(data.contents).words;
        if (words) {
          words = words.filter((w) => {
            w.heading = this.parseOptionsHeading(w.heading);
            return w.heading;
          });
          this.options = words ?? "";
        } else {
          this.options = "";
        }
      } catch (error) {
        this.optionsError = true;
        this.fetchingOptions = false;
      }
    },
    parseOptionsHeading(heading) {
      const weirdChar = /○|🄰/;
      const afterParenthesis = /\[.*/;
      heading = heading.replace(afterParenthesis, "").replace(weirdChar, "");
      return heading;
    },
    fetchDefinition(term) {
      this.currentSearch = term.heading;
      this.japRes = this.parseJap(term);

      const hiraganaOrKatakana = term.heading
        .replace(/【(.*)】/, "")
        .replace(/‐/g, "");
      const kanjiInSquareBrackets = term.heading.match(/【(.*)】/);
      this.fetchEng(
        kanjiInSquareBrackets ? kanjiInSquareBrackets[1] : hiraganaOrKatakana
      );
    },
    async fetchEng(term) {
      this.fetchingEng = true;
      this.engRes = null;
      const uri = `https://www.edrdg.org/cgi-bin/wwwjdic/wwwjdic?1ZUJ${encodeURIComponent(
        term
      )}`;
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(uri)}`
      );

      try {
        const data = await response.json();
        this.fetchingEng = false;
        this.engRes = this.parseEng(data);
      } catch (error) {
        console.log(error);
        this.fetchingEng = false;
        this.engRes = { heading: "ERROR", body: "NO RESULTS FOUND" };
      }
    },
    parseJap(option) {
      const text = option.text.replace(/\[([^\]]+)\]/g, "");

      const [, ...body] = text.split("\n").filter((l) => {
        if (l.match(/[⇒→]/) || l.match(/^\(.*\)$/)) {
          return false;
        }
        return l;
      });

      return { heading: option.heading, body };
    },
    parseEng(rawRes) {
      const htmlData = parser
        .parseFromString(rawRes.contents, "text/html")
        .querySelector("pre");

      const htmlRes = htmlData?.innerText.split("\n") ?? null;

      const definition = htmlRes.map((r) => {
        r = r
          .replace(/\//, " ")
          .replace(/\/$/, "")
          .replace(/(?<=\w)(\/)(?=\w)/g, ", ")
          .replace(/\/\(P\)/, "")
          .replace(/]/, "$&\n")
          .replace(/\/(\()/g, "\n$1");

        const [heading, body] = r.split("\n");

        const isPerfectResult = heading.includes(
          this.currentSearch.match(/【(.*)】/)[1]
        );

        return { heading, body, isPerfectResult };
      });

      return definition[0];
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

/* credits */

#credits {
  position: relative;
  display: flex;
  margin-top: auto;
  padding: 0.5rem 1rem;
  height: 4vh;
  align-items: center;
}
#github {
  text-decoration: none;
  color: #333;
  white-space: nowrap;
  display: inline-block;
}
#github svg {
  height: 2vh;
  width: auto;
  display: block;
}
#logo {
  height: 80%;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  margin: 0 auto;
}
</style>