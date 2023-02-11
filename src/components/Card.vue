<template>
  <div class="threed-space">
    <div class="flipper">
      <div @click="flipCard" :class="classes">
        <div class="front">
          <div class="card-header">
            {{ name.charAt(0).toUpperCase() + name.slice(1) }}
          </div>
          <div class="card-image">
            <img :src="imageSrc" />
          </div>
        </div>
        <div class="back">
          <div class="card-body">
            <ul>
              <li v-for="stat in stats" :key="stat.name">
                {{ stat }}
              </li>
            </ul>
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
      isFlipped: false,
    };
  },
  methods: {
    flipCard() {
      this.isFlipped = !this.isFlipped;
    },
  },
  computed: {
    classes() {
      return {
        flipped: this.isFlipped,
        card: true,
        flipper: true,
      };
    },
  },
  props: {
    stats: {
      type: Array,
      required: true,
    },
    name: {
      type: String,
      required: true,
    },
    imageSrc: {
      type: String,
      required: true,
    },
  },
};
</script>

<style>
.threed-space {
  height: 400px;
  width: 300px;
  perspective: 1000px;
}
.flipper {
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 1600ms;
  position: relative;
  cursor: pointer;
}

.flipper.flipped {
  transform: rotateY(180deg);
}

.front {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: white;
  backface-visibility: hidden;
  display: flex;
  flex-direction: column;
}
.back {
  transform: rotateY(180deg);
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  align-items: center;
}
#switch {
  display: none;
}

.card {
  display: block;
  width: 100%;
  height: 100%;
  background: white;
  border: 12px solid grey;
  border-radius: 10px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
}

.card-header {
  color: black;
  padding: 20px 10px;
  height: 15%;
  width: 93%;
  border-radius: 5px;
  margin: 10px 10px 10px 10px;
  text-align: center;
  box-shadow: 0px 0px 0px 2px black, 0px 0px 0px 4px white,
    0px 0px 0px 7px black;
}

.card-image {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 85%;
  margin: 10px;
  border-radius: 5px;
  box-shadow: 0px 0px 0px 2px black, 0px 0px 0px 4px white,
    0px 0px 0px 7px black;
}

.card-body {
  padding: 10px;
}

.card-body ul {
  list-style-type: none;
}

img {
  height: auto;
  width: 100%;
  margin-left: 18px;
}

ul {
  text-align: center;
}

li {
  padding: 5px 0px;
}
</style>