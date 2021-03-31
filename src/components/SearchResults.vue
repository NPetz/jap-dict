<template>
  <div id="hits-wrapper">
    <transition name="fadeIn">
      <ul id="hits-container" v-if="prop" @mousemove="customScroll">
        <li
          v-for="(entry, index) in prop"
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
  data: () => ({}),
  methods: {
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

      e.target.classList.add("active");
    },

    customScroll(e) {
      let el = e.currentTarget;
      let x = e.clientX - 16;
      let totalWidth = el.scrollWidth;
      let scroll = el.scrollLeft;
      let width = el.offsetWidth;

      let scrollDirection = x > width / 2 ? "right" : "left";
      let magnitude =
        Math.abs(width / 2 - x) > width * 0.4 ? totalWidth * 0.01 : 0;

      if (scrollDirection === "right") {
        el.scrollLeft = scroll <= totalWidth ? scroll + magnitude : totalWidth;
      } else {
        el.scrollLeft = scroll >= 0 ? scroll - magnitude : 0;
      }
    },
  },
};
</script>

<style scoped>
#hits-wrapper {
  position: relative;
}
#hits-wrapper::after {
  content: "";
  width: 5%;
  height: 100%;
  position: absolute;
  right: 0;
  top: 0;
  background: linear-gradient(270deg, #eee, transparent);
  pointer-events: none;
}
#hits-wrapper::before {
  content: "";
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
  margin: 1rem;
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
  font-size: 1.2rem;
  padding: 1rem;
  color: #333;
  background-color: #fff;
  border: 1px solid #333;
  border-bottom: 3px solid #333;
  white-space: nowrap;
  cursor: pointer;
  transition: all 0.2s;
}

.hits:hover,
.hits.active {
  color: #eee;
  background-color: #333;
}
.hits.active {
  color: #eee;
  background-color: #333;
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
