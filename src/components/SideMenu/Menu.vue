<template>
  <div>
    <div id="sideNav" class="bm-menu">
      <nav class="bm-item-list">
        <slot></slot>
      </nav>
      <span class="bm-cross-button cross-style" @click="closeMenu" :class="{ hidden: !crossIcon }">
        <span
          v-for="(x, index) in 2"
          :key="x"
          class="bm-cross"
          :style="{ position: 'absolute', width: '3px', height: '14px',transform: index === 1 ? 'rotate(45deg)' : 'rotate(-45deg)'}"
        ></span>
      </span>
    </div>

    <div class="bm-burger-button" @click="openMenu" :class="{ hidden: !burgerIcon }">
      <font-awesome-icon icon="bars"/>
    </div>
  </div>
</template>

<script>
export default {
  name: "menubar",
  data() {
    return {
      isSideBarOpen: false
    };
  },
  props: {
    isOpen: {
      type: Boolean,
      required: false
    },
    right: {
      type: Boolean,
      required: false
    },
    width: {
      type: [String],
      required: false,
      default: "300"
    },
    disableEsc: {
      type: Boolean,
      required: false
    },
    noOverlay: {
      type: Boolean,
      required: false
    },
    onStateChange: {
      type: Function,
      required: false
    },
    burgerIcon: {
      type: Boolean,
      required: false,
      default: true
    },
    crossIcon: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  methods: {
    openMenu() {
      this.$emit("openMenu");
      this.isSideBarOpen = true;

      if (!this.noOverlay) {
        document.body.className += "bm-overlay";
      }
      if (this.right) {
        document.querySelector(".bm-menu").style.left = "auto";
        document.querySelector(".bm-menu").style.right = "0px";
      }
      this.$nextTick(function() {
        document.getElementById("sideNav").style.width = this.width
          ? this.width + "px"
          : "300px";
      });
    },

    closeMenu() {
      this.$emit("closeMenu");
      this.isSideBarOpen = false;
      document.body.className = document.body.className.replace(
        "bm-overlay",
        ""
      );
      document.getElementById("sideNav").style.width = "0px";
    },

    closeMenuOnEsc(e) {
      e = e || window.event;
      if (e.key === "Escape" || e.keyCode === 27) {
        document.getElementById("sideNav").style.width = "0px";
        document.body.style.backgroundColor = "inherit";
        this.isSideBarOpen = false;
      }
    },
    documentClick(e) {
      let element = document.querySelector(".bm-burger-button");
      let target = null;
      if (e && e.target) {
        target = e.target;
      }

      if (
        element &&
        element !== target &&
        !element.contains(target) &&
        e.target.className !== "bm-menu" &&
        this.isSideBarOpen
      ) {
        this.closeMenu();
      }
    }
  },

  mounted() {
    if (!this.disableEsc) {
      document.addEventListener("keyup", this.closeMenuOnEsc);
    }
  },
  created: function() {
    document.addEventListener("click", this.documentClick);
  },
  destroyed: function() {
    document.removeEventListener("keyup", this.closeMenuOnEsc);
    document.removeEventListener("click", this.documentClick);
  },
  watch: {
    isOpen: {
      deep: true,
      immediate: true,
      handler(newValue, oldValue) {
        if (!oldValue && newValue) {
          this.openMenu();
        }
        if (oldValue && !newValue) {
          this.closeMenu();
        }
      }
    },
    right: {
      deep: true,
      immediate: true,
      handler(oldValue, newValue) {
        if (oldValue) {
          this.$nextTick(() => {
            document.querySelector(".bm-burger-button").style.left = "auto";
            document.querySelector(".bm-burger-button").style.right = "36px";
            document.querySelector(".bm-menu").style.left = "auto";
            document.querySelector(".bm-menu").style.right = "0px";
          });
        }
        if (newValue) {
          if (
            document.querySelector(".bm-burger-button").hasAttribute("style")
          ) {
            document
              .querySelector(".bm-burger-button")
              .removeAttribute("style");
            document.getElementById("sideNav").style.right = "auto";
          }
        }
      }
    }
  }
};
</script>

<style>
html {
  height: 100%;
}
.bm-burger-button {
  position: sticky;
  width: 36px;
  height: 30px;
  left: 36px;
  top: 36px;
  cursor: pointer;
  float: right;
}
.bm-burger-button.hidden {
  display: none;
}
.bm-burger-bars {
  background-color: #f4f4f4;
}
.line-style {
  position: absolute;
  height: 20%;
  left: 0;
  right: 0;
}
.cross-style {
  position: absolute;
  top: 12px;
  right: 2px;
  cursor: pointer;
}
.bm-cross {
  background: #bdc3c7;
}
.bm-cross-button {
  height: 30px;
  width: 30px;
}
.bm-cross-button.hidden {
  display: none;
}
.bm-menu {
  height: 100%; /* 100% Full-height */
  width: 0; /* 0 width - change this with JavaScript */
  position: fixed; /* Stay in place */
  z-index: 1000; /* Stay on top */
  top: 0;
  left: 0;
  background-color: rgb(63, 63, 65); /* Black*/
  overflow-x: hidden; /* Disable horizontal scroll */
  padding-top: 60px; /* Place content 60px from the top */
  transition: 0.2s; /*0.5 second transition effect to slide in the sidenav*/
}

.bm-overlay {
  background: rgba(0, 0, 0, 0.3);
}
.bm-item-list {
  color: #b8b7ad;
  /* margin-left: 10%; */
  font-size: 20px;
}
.bm-item-list > * {
  display: flex;
  text-decoration: none;
  padding: 0.7em;
}
.bm-item-list > * > span {
  margin-left: 10px;
  font-weight: 700;
  color: white;
}
</style>

