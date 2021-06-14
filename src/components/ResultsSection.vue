<template>
  <section id="results" v-if="engRes || japRes">
    <section id="japRes">
      <article class="resultWrapper" v-if="japRes">
        <h2 class="languageLabel">日本語</h2>
        <div class="definition">
          <h2 class="heading">{{ japRes.heading }}</h2>
          <p v-for="(line, index) in japRes.body" :key="index" class="line">
            {{ line }}
          </p>
        </div>
      </article>
    </section>
    <section id="engRes">
      <article v-if="engRes || fetchingEng" class="resultWrapper">
        <h2 class="languageLabel">English</h2>

        <div class="loader" v-if="fetchingEng"></div>
        <div class="definition" v-if="!fetchingEng">
          <i v-if="!engRes.isPerfectResult" class="accuracyLabel"
            ><svg
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              version="1.1"
              id="Capa_1"
              x="0px"
              y="0px"
              viewBox="0 0 486.463 486.463"
              style="enable-background: new 0 0 486.463 486.463"
              xml:space="preserve"
            >
              <g>
                <g>
                  <path
                    d="M243.225,333.382c-13.6,0-25,11.4-25,25s11.4,25,25,25c13.1,0,25-11.4,24.4-24.4    C268.225,344.682,256.925,333.382,243.225,333.382z"
                  />
                  <path
                    d="M474.625,421.982c15.7-27.1,15.8-59.4,0.2-86.4l-156.6-271.2c-15.5-27.3-43.5-43.5-74.9-43.5s-59.4,16.3-74.9,43.4    l-156.8,271.5c-15.6,27.3-15.5,59.8,0.3,86.9c15.6,26.8,43.5,42.9,74.7,42.9h312.8    C430.725,465.582,458.825,449.282,474.625,421.982z M440.625,402.382c-8.7,15-24.1,23.9-41.3,23.9h-312.8    c-17,0-32.3-8.7-40.8-23.4c-8.6-14.9-8.7-32.7-0.1-47.7l156.8-271.4c8.5-14.9,23.7-23.7,40.9-23.7c17.1,0,32.4,8.9,40.9,23.8    l156.7,271.4C449.325,369.882,449.225,387.482,440.625,402.382z"
                  />
                  <path
                    d="M237.025,157.882c-11.9,3.4-19.3,14.2-19.3,27.3c0.6,7.9,1.1,15.9,1.7,23.8c1.7,30.1,3.4,59.6,5.1,89.7    c0.6,10.2,8.5,17.6,18.7,17.6c10.2,0,18.2-7.9,18.7-18.2c0-6.2,0-11.9,0.6-18.2c1.1-19.3,2.3-38.6,3.4-57.9    c0.6-12.5,1.7-25,2.3-37.5c0-4.5-0.6-8.5-2.3-12.5C260.825,160.782,248.925,155.082,237.025,157.882z"
                  />
                </g>
              </g></svg
          ></i>
          <h2 class="heading">{{ engRes.heading }}</h2>
          <p class="line">
            {{ engRes.body }}
          </p>
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
  position: relative;
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

.heading {
  font-size: 1.3rem;
  margin-bottom: 0.5rem;
}

.line {
  margin-bottom: 0.3rem;
}

.definition.collapsed {
  display: none;
}

.accuracyLabel {
  position: absolute;
  top: 0;
  right: 0;
  background-color: #ffd166;
  padding: 0.5rem;
  margin: 0.5rem;
  width: 2rem;
  height: 2rem;
}
.accuracyLabel svg {
  display: block;
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