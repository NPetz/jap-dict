<template>
  <section id="results" v-if="engRes || japRes">
    <section id="japRes">
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
              @click="$emit('search', entry)"
            >
              {{ entry }}
            </li>
          </ul>
        </div>
      </article>
    </section>
    <section id="engRes">
      <article v-if="engRes || fetchingEng" class="resultWrapper">
        <h2 class="languageLabel">English</h2>
        <div class="loader" v-if="fetchingEng"></div>
        <div class="definition">
          <div v-if="engRes" v-html="engRes"></div>
          <p v-if="errorEng">ERROR - TRY AGAIN</p>
        </div>
      </article>
    </section>
  </section>
</template>

<script>
export default {
  name: "ResultsSection",
  data: () => ({
    throttling: false,
  }),
  props: ["engRes", "japRes", "fetchingEng", "errorEng"],
  methods: {
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
#results {
  padding: 0 1rem;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  overflow-y: auto;
  overflow-x: hidden;
  gap: 1rem;
  height: 100%;
  background: linear-gradient(
    90deg,
    transparent calc(50% - 1px),
    #333 calc(50%),
    transparent calc(50% + 1px)
  );
}
#results::-webkit-scrollbar {
  height: 10px;
  background-color: #eee;
  display: none;
}
#japRes,
#engRes {
  display: flex;
  flex-direction: column;
  justify-content: center;
  max-width: 100%;
  padding-bottom: 0.5rem;
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
  align-self: flex-start;
  margin: 0.5rem 0 0.5rem 0.5rem;
  align-items: center;
  background-color: #333;
  color: #eee;
  padding: 0 0.5rem;
  font-size: 1rem;
  height: 2rem;
}

.definition {
  word-break: break-word;
  white-space: pre-line;
  font-size: 1.3em;
  padding: 0.5rem 3rem 2rem;
  width: 100%;
  max-width: 600px;
}
.definition.collapsed {
  display: none;
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

@media screen and (min-width: 740px) {
  #results {
    flex-direction: row;
    flex-wrap: nowrap;
    background: none;
  }
  #japRes,
  #engRes {
    overflow: auto;
    display: block;
    height: 100%;
    flex: 1 0 0;
    background: linear-gradient(
      90deg,
      transparent calc(50% - 1px),
      #333 calc(50%),
      transparent calc(50% + 1px)
    );
  }

  #japRes::-webkit-scrollbar,
  #engRes::-webkit-scrollbar {
    height: 10px;
    background-color: #eee;
    display: none;
  }
}
</style>