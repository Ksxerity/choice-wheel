<template>
  <div class="container">
    <div
      class="wheel-container"
      :style="`
        --diameter: ${DIAMETER}px;
        --pointer: ${pointer}deg;
      `"
    >
      <div
        ref="wheel"
        :class="`wheel ${rotate ? 'spin' : null}`"
        v-on:click="rotationController"
      >
        <div
          class="wedge"
          :style="`
            transform: rotate(${wedgeAngle * (index - 1)}deg);
            border-color: transparent ${colorController(
              index
            )} transparent transparent;
            --radius: ${RADIUS}px;
            --wedge-perimeter: ${wedgePerimeter}px;
            --wedge-offset: -${wedgePerimeter - 1}px;
          `"
          v-for="index in slots"
          :key="`wedge${index}`"
        >
          <label
            class="wedge-label"
            :style="`--label-height: ${wedgePerimeter * 2}px`"
            >{{ entries[index - 1] }}</label
          >
        </div>
      </div>
      <div class="ticker"></div>
    </div>
    <div class="settings">
      <input
        type="text"
        class="entry"
        :style="`
          grid-column-start: 1;
          grid-row-start: ${index};
          background-color: white;
        `"
        v-for="index in slots"
        :key="`entry${index}`"
        v-model="entries[index - 1]"
      />
      <button
        class="btn btn-danger"
        :style="`
          grid-column-start: 2;
          grid-row-start: ${index}
        `"
        v-for="index in slots"
        :key="`remove${index}`"
        v-on:click="handleRemoveEntry(index - 1)"
      >
        Remove
      </button>
      <button
        class="btn btn-primary"
        :style="`
          grid-column-start: 1;
          grid-column-end: 3;
          grid-row-start: ${slots + 1}
        `"
        v-on:click="handleAddEntry"
      >
        Add Entry
      </button>
    </div>
    <div id="myModal" class="modal" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-body">
            <p>
              Entry chosen was {{ entries[winningEntryIndex] }}. Remove entry?
            </p>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-danger"
              v-on:click="handleRemoveEntry(winningEntryIndex)"
            >
              Remove
            </button>
            <button type="button" class="btn btn-primary" data-dismiss="modal">
              Keep
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      slots: 6,
      entries: ['entry 1', 'entry 2', 'entry 3', 'entry 4', 'entry 5', 'entry 6'],
      pointer: 0,
      rotate: false,
      keyframes: null,
      winningEntryIndex: 0,
    };
  },
  created() {
    this.DIAMETER = 800;
    this.RADIUS = this.DIAMETER / 2 + 10; // + 10 just for extra pixels to manipulate margin with
    this.MIN_SLOTS = 3;
    this.MAX_SLOTS = 19;
  },
  mounted() {
    this.$nextTick(() => {
      const myRules = document.styleSheets[0].cssRules;
      for (let i = 0; i < myRules.length; i += 1) {
        if (
          myRules[i].type === window.CSSRule.KEYFRAMES_RULE ||
          myRules[i].type === window.CSSRule.WEBKIT_KEYFRAMES_RULE
        ) {
          this.keyframes = myRules[i];
        }
      }
    });
  },
  computed: {
    wedgePerimeter() {
      // Not really calculating the perimeter here. Since we are using triangles to create
      // the wedges, the perimeter is not applicable here. So we have to create a larger
      // triangle that will encompass the whole area of the wedge.
      const angle = 360 / this.slots / 2;
      return Math.ceil(Math.tan((angle * Math.PI) / 180) * this.RADIUS);
    },
    wedgeAngle() {
      return 360 / this.slots;
    },
  },
  methods: {
    handleAddEntry() {
      if (!this.rotate && this.slots !== this.MAX_SLOTS) {
        this.slots += 1;
        this.entries.push(`entry ${this.slots}`);
      }
    },
    handleRemoveEntry(index) {
      if (!this.rotate) {
        if (this.slots === this.MIN_SLOTS) {
          this.entries.splice(index, 1, '');
        } else {
          this.entries.splice(index, 1);
          this.slots -= 1;
        }
        this.$('#myModal').modal('hide');
      }
    },
    colorController(index) {
      if (this.slots % 2 === 1 && index === this.slots) {
        return '#519492';
      }
      return index % 2 === 0 ? '#5eaaa8' : '#a3d2ca';
    },
    rotationController() {
      if (!this.rotate) {
        const randomSelection = Math.floor(Math.random() * 360);
        const rotateAmount = this.pointer + randomSelection + 1440;
        this.keyframes.deleteRule('0%');
        this.keyframes.deleteRule('100%');
        this.keyframes.appendRule(`0% { transform: rotate(${this.pointer}deg) }`);
        this.keyframes.appendRule(`100% { transform: rotate(${rotateAmount}deg) }`);
        this.applyRotation(rotateAmount % 360);
      }
    },
    applyRotation(ptr) {
      this.rotate = true;
      setTimeout(() => {
        this.pointer = ptr;
        this.rotate = false;
        const ptrOffset = this.wedgeAngle / 2; // The ticker sits in the middle of the first wedge
        // Wheel spins in the opposite direction of the order of indexes
        // So we have to take the entry from the opposite end
        this.winningEntryIndex =
          this.entries.length - Math.floor((ptr + ptrOffset) / this.wedgeAngle);
        if (this.winningEntryIndex === this.slots) {
          this.winningEntryIndex = 0;
        }
        this.$('#myModal').modal('show');
      }, 3000);
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
.wheel-container {
  position: relative;
  height: var(--diameter);
  width: var(--diameter);
  margin-right: 100px;
  min-width: var(--diameter);
}
@keyframes rotation {
}
.spin {
  animation: rotation 3s;
  animation-fill-mode: forwards;
}
.wheel {
  transform: rotate(var(--pointer));
  background-color: white;
  height: inherit;
  border-radius: 50%;
  overflow: hidden;
}
.wedge {
  position: absolute;
  top: 50%;
  left: 50%;
}
.wedge::before {
  content: '';
  position: absolute;
  transform-origin: top left;
  height: 0;
  width: 0;
  margin-left: -1px;
  border-bottom: var(--wedge-perimeter) solid;
  border-right: var(--radius) solid;
  border-color: inherit;
}
.wedge::after {
  content: '';
  position: absolute;
  transform-origin: bottom left;
  height: 0;
  width: 0;
  margin-left: -1px;
  margin-top: var(--wedge-offset);
  border-top: var(--wedge-perimeter) solid;
  border-right: var(--radius) solid;
  border-color: inherit;
}
.wedge-label {
  position: absolute;
  display: flex;
  align-items: center;
  z-index: 10;
  font-size: 2em;
  height: var(--label-height);
  margin-top: var(--wedge-offset);
  margin-left: 200px;
  width: 190px;
  white-space: nowrap;
  text-overflow: clip;
  overflow: hidden;
}
.ticker {
  position: absolute;
  width: 0;
  height: 0;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-right: 30px solid black;
  top: 390px; /* radius minus the pixels for border-top */
  left: 99%;
}
.slots-entry {
  margin-left: 8px;
  width: 15%;
}
.settings {
  display: grid;
  grid-template-columns: 150px 90px;
  grid-template-rows: auto;
  column-gap: 2px;
  row-gap: 2px;
  border: 2px solid black;
  background-color: gray;
}
</style>
