<template>
  <div
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
  >
    <ul v-if="options !== null">
      <transition-group name="fadeIn">
        <OptionCell
          v-for="(entry, index) in options"
          :key="index"
          @click="handleClick($event, entry)"
        >
          {{ parseOptionHeading(entry.heading) }}
        </OptionCell>
        <ErrorCell v-if="!options && !fetching">
          {{ error ? "ERROR - TRY AGAIN" : "NO RESULTS" }}
        </ErrorCell>
      </transition-group>
    </ul>
  </div>
</template>


<script>
import ErrorCell from "./ErrorCell.vue";
import OptionCell from "./OptionCell.vue";

export default {
  name: "SearchOptions",
  props: ["options", "fetching", "error"],
  components: {
    OptionCell,
    ErrorCell,
  },
  data: () => ({
    scrollInterval: null,
    mousePosition: null,
    throttling: false,
  }),
  watch: {
    options(newV, oldV) {
      if (oldV !== newV && !this.fetching && !this.error) {
        setTimeout(() => {
          let hits = document.querySelector(".hit");
          hits.click();
        }, 1000);
      }
    },
  },
  methods: {
    // TODO tweak this one to be more universal
    parseOptionHeading(text) {
      const afterParenthesis = /\[.*/;
      text = text.replace(afterParenthesis, "");
      return text;
    },
    handleClick(e, entry) {
      this.$emit("fetch-definition", entry);
      this.toggleActive(e);
    },
    toggleActive(e) {
      let hits = document.querySelectorAll(".hit");
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
#hits-container {
  width: 100%;
  min-height: 50px;
}

#hits-container ul {
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
}

#hits-container ul::-webkit-scrollbar {
  height: 10px;
  background-color: #eee;
  display: none;
}
#hits-container ul::-webkit-scrollbar-track {
  background-color: #aaa;
}
#hits-container ul::-webkit-scrollbar-thumb {
  background-color: #333;
}

.errorHit {
  cursor: auto;
}

/* transitions */
.fadeIn-enter-active {
  transition: all 1s ease;
}
.fadeIn-leave-active {
  transition: all 1s cubic-bezier(1, 0.5, 0.8, 1);
}
.fadeIn-enter, .fadeIn-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(100%);
  opacity: 0;
}
</style>
