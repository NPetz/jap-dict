<template>
  <div id="hits-wrapper">
    <transition name="fadeIn">
      <ul
        id="hits-container"
        @mouseenter="scrollEnter"
        @mouseleave="scrollLeave"
        @mousemove="
          (e) => {
            if (!throttling) {
              mousePosition = e.x;
            } else {
              throttling = true;
              setTimeout(() => (throttling = false), 300);
            }
          }
        "
        v-if="prop.results !== false"
      >
        <li class="hits noResults" v-if="prop.results === null">
          <p>NO RESULTS</p>
        </li>
        <li
          v-for="(entry, index) in prop.results"
          :key="index"
          class="hits"
          @click="
            $emit('fetch-definition', entry);
            toggleActive($event);
          "
        >
          <p class="kanji">
            {{ parseResults(entry.heading) }}
          </p>
        </li>
      </ul>
    </transition>
  </div>
</template>

<script>
export default {
  name: "SearchResults",
  props: ["prop"],
  data: () => ({
    scrollInterval: null,
    mousePosition: null,
    throttling: false,
  }),
  watch: {
    prop(newV, oldV) {
      if (oldV.fetchingOptions && !newV.fetchingOptions && newV.results) {
        setTimeout(() => {
          let hits = document.querySelector(".hits");
          hits.click();
        }, 800);
      }
    },
  },
  methods: {
    // TODO tweak this one to be more universal
    parseResults(text) {
      const afterParenthesis = /\[.*/;
      text = text.replace(afterParenthesis, "");

      return text;
    },

    toggleActive(e) {
      let hits = document.querySelectorAll(".hits");
      hits.forEach((x) => {
        x.classList.remove("active");
      });

      e.currentTarget.classList.add("active");

      let el = e.currentTarget;

      setTimeout(() => {
        el.scrollIntoView({
          block: "center",
          behavior: "smooth",
          inline: "center",
        });
      }, 400);
    },
    scrollEnter(e) {
      let target = e.currentTarget;
      this.scrollInterval = setInterval(() => {
        if (this.mousePosition < window.innerWidth * 0.15) {
          target.scrollLeft -= 25;
        } else if (
          this.mousePosition >
          window.innerWidth - window.innerWidth * 0.15
        ) {
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

<style scoped>
#hits-wrapper {
  position: relative;
}
#hits-wrapper::after {
  /* content: ""; */
  width: 5%;
  height: 100%;
  position: absolute;
  right: 0;
  top: 0;
  background: linear-gradient(270deg, #eee, transparent);
  pointer-events: none;
}
#hits-wrapper::before {
  /* content: ""; */
  width: 5%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  background: linear-gradient(90deg, #eee, transparent);
  pointer-events: none;
  z-index: 1;
}

#hits-container {
  display: flex;
  overflow-x: auto;
  flex-wrap: nowrap;
  list-style: none;
  align-content: space-around;
  gap: 1rem;
  background: linear-gradient(
    180deg,
    transparent calc(50% - 1px),
    #333 calc(50%),
    transparent calc(50% + 1px)
  );
  /* margin: 1rem 0rem; */
}

#hits-container::-webkit-scrollbar {
  height: 10px;
  background-color: #eee;
  display: none;
}
#hits-container::-webkit-scrollbar-track {
  background-color: #aaa;
}
#hits-container::-webkit-scrollbar-thumb {
  background-color: #333;
}

.hits {
  display: flex;
  position: relative;
  align-items: flex-end;
  justify-content: center;
  flex: 1 0 auto;
  font-size: 1em;
  padding: 0.5rem 1rem;
  color: #333;
  background-color: #fff;
  border: 1px solid #333;
  border-bottom: 3px solid #333;
  white-space: nowrap;
  cursor: pointer;
  transition: all 0.2s;
  min-width: 0px;
  user-select: none;
}

.hits:hover,
.hits.active {
  color: #eee;
  background-color: #333;
}
.hits.active {
  color: #eee;
  background-color: #333;
  min-width: 80%;
}

.yomi {
  display: none;
}

/* transitions */
.fadeIn-enter-active {
  transition: all 1s ease;
}
.fadeIn-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}
.fadeIn-enter, .fadeIn-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(100%);
  opacity: 0;
}
</style>
