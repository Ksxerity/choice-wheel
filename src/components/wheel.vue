<template>
  <div class="wheel" :style="'--diameter: ' + diameter + 'px'">
    <div
      class="wedge"
      :style="`
        transform: rotate(${calculateAngle(index)}deg);
        --radius: ${radius}px;
        --wedge-perimeter: ${wedgePerimeter}px;
      `"
      v-for="index in slots"
      :key="index"
    ></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      slots: 6,
      diameter: 800,
    };
  },
  computed: {
    radius() {
      return this.diameter / 2;
    },
    wedgePerimeter() {
      // Not really calculating the perimeter here. Since we are using triangles to create
      // the wedges, the perimeter is not applicable here. So we have to create a larger
      // triangle that will encompass the whole area of the wedge.
      const angle = 360 / this.slots / 2;
      return Math.tan((angle * Math.PI) / 180) * this.radius;
    },
  },
  methods: {
    calculateAngle(index) {
      const deg = 360 / this.slots;
      return deg * index;
    },
  },
};
</script>

<style scoped>
.wheel {
  position: relative;
  margin: 0 auto;
  height: var(--diameter);
  width: var(--diameter);
  background-color: gray;
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
  border-bottom: var(--wedge-perimeter) solid transparent;
  border-right: var(--radius) solid orange;
}
.wedge::after {
  content: '';
  position: absolute;
  transform-origin: bottom left;
  height: 0;
  width: 0;
  margin-top: calc(-1 * var(--wedge-perimeter));
  border-top: var(--wedge-perimeter)  solid transparent;
  border-right: var(--radius) solid green;
}
</style>
