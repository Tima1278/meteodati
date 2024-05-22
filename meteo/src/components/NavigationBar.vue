<template>
  <nav>
    <ul>
      <li 
        v-for="(item, index) in navItems" 
        :key="index" 
        @mouseover="hoverEffect(index, true)"
        @mouseleave="hoverEffect(index, false)"
        @click="navigate(item)"
        :class="{ 'active': selectedIndex === index, 'hover': hoverIndex === index }"
      >
        {{ item }}
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  data() {
    return {
      navItems: ["Excel", "Grafici", "Città più calde", "Crea", "Aggiorna"],
      hoverIndex: null,
      selectedIndex: null
    };
  },
  methods: {
    navigate(item) {
      this.selectedIndex = this.navItems.indexOf(item);
      this.$emit("navigate", item);
    },
    hoverEffect(index, isHover) {
      if (isHover) {
        this.hoverIndex = index;
      } else {
        this.hoverIndex = null;
      }
    }
  }
};
</script>

<style scoped>
nav {
  background-color: #000;
  color: #fff;
  padding: 10px 0;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: space-around;
}

li {
  cursor: pointer;
  padding: 10px;
  transition: background-color 0.3s;
  border-radius: 20px;
}

li:hover {
  background-color: #555;
}

li.active {
  background-color: #000;
}

li.hover {
  background-color: #666;
}
</style>
