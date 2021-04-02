<template>
  <div class="container">
    <div class="wheel" :style="'--diameter: ' + diameter + 'px'">
      <div
        class="wedge"
        :style="`
          transform: rotate(${wedgeAngle * index}deg);
          border-color: transparent ${colorController(
            index
          )} transparent transparent;
          --radius: ${radius}px;
          --wedge-perimeter: ${wedgePerimeter}px;
          --wedge-offset: -${wedgePerimeter - 1}px;
        `"
        v-for="index in slots"
        :key="index"
      >
        <label
          class="wedge-label"
          :style="`--label-height: ${wedgePerimeter * 2}px`"
        >
          asd
        </label>
      </div>
    </div>
    <div class="settings">
      <label>How many entries?</label>
      <input
        class="slots-entry"
        type="number"
        v-model="slotsHolder"
        :min="minSlots"
        :max="maxSlots"
      />
      <button v-on:click="handleSubmit">Submit</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      slots: 6,
      slotsHolder: 6,
      diameter: 800,
      minSlots: 3,
      maxSlots: 19,
    };
  },
  computed: {
    radius() {
      // + 10 just for extra pixels to manipulate margin with
      return this.diameter / 2 + 10;
    },
    wedgePerimeter() {
      // Not really calculating the perimeter here. Since we are using triangles to create
      // the wedges, the perimeter is not applicable here. So we have to create a larger
      // triangle that will encompass the whole area of the wedge.
      const angle = 360 / this.slots / 2;
      return Math.ceil(Math.tan((angle * Math.PI) / 180) * this.radius);
    },
    wedgeAngle() {
      return 360 / this.slots;
    },
  },
  methods: {
    handleSubmit() {
      const val = parseInt(this.slotsHolder, 10);
      if (val >= this.maxSlots) {
        this.slots = this.maxSlots;
        this.slotsHolder = this.maxSlots;
      } else if (val <= this.minSlots) {
        this.slots = this.minSlots;
        this.slotsHolder = this.minSlots;
      } else {
        this.slots = parseInt(this.slotsHolder, 10);
      }
    },
    colorController(index) {
      if (this.slots % 2 === 1 && index === this.slots) {
        return '#9ba4b4';
      }
      return index % 2 === 0 ? '#14274e' : '#394867';
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
.wheel {
  position: relative;
  margin: 0 auto;
  height: var(--diameter);
  width: var(--diameter);
  min-width: var(--diameter);
  background-color: white;
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
  margin-left: 300px;
}
.slots-entry {
  margin-left: 8px;
  width: 15%;
}
</style>
