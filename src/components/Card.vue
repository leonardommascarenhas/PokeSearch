<template>
  <div class="card" @click="displayBlock = !displayBlock">
    <div class="card-header">
      {{ name }}
    </div>
    <img :src="imageSrc" />
    <div class="card-body" v-show="displayBlock">
      <ul>
        <li v-for="stat in stats" :key="stat.stat.name">
          {{ stat.stat.name }}: {{ stat.base_stat }}
        </li>
      </ul>
    </div>
  </div>
</template> 

<script>
export default {
  methods: {
    displayStats(element) {
      return (element.display = "block");
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
  data() {
    return {
      displayBlock: false,
    };
  },
};
</script>

<style>
.card {
  min-width: 200px;
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  border-radius: 5px;
  margin: 20px;
  transition: 0.25s;
}
.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
  transform: translateY(-5px);
}
.card-header {
  background-color: lightblue;
  color: white;
  padding: 10px;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
  width: 100%;
}

.card-body {
  padding: 10px;
}

.card-body ul {
  list-style-type: none;
}
</style>
