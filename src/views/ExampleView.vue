<script setup>
import { ref } from 'vue'
const showMenu = ref(false)
const selectedKeys = ref({})

const getRandomInt = (max) => {
  return Math.floor(Math.random() * max);
}
const boxs = ref(Array.from({ length: 9 }, (_, index) => ({
  key: index + 1,
  isFlashing: getRandomInt(3) === 0
})));
const balls = ref([1, 3, 7, 9])
const menuData = ref([]);
const onFetchMockData = async () => {
  try {
    const response = await fetch('/src/assets/data.json');
    const data = await response.json();
    menuData.value = data;
  } catch (error) {
    console.error('Error fetching menu data:', error);
  }
};

onFetchMockData()

const openMenu = () => {
  showMenu.value = true
}

const closeMenu = () => {
  showMenu.value = false
  selectedKeys.value = {}
}

const handleClickOutside = (event) => {
  if (!event.target.closest('.menu') && showMenu.value) {
    closeMenu()
  }
}

const selectMenuItem = (item) => {
  selectedKeys.value = item
}

</script>

<template>
  <div class="wrapper" @click="handleClickOutside">
    <nav class='nav'>
      <div class="menu-icon-container" @click.stop="openMenu" >
        <img src='/src/assets/icon-header-menu.svg' />
      </div>
    </nav>
    <div class="container">
      <div class="content">
        <div v-for="{key, isFlashing} in boxs" :key="key" class="box">
          <div :class="{'flashing': isFlashing}" class="box-content"></div>
          <div class="ball-container" v-if="balls.includes(key)">
            <div class="ball">0</div>
          </div>
        </div>
      </div>
    </div>
    <div
      class="menu"
      :class="{ 'show-menu': showMenu }"
      @click="closeMenu"
    >
      <ul class="menu-list" >
        <li v-for="{ key, text, children } in menuData" class='menu-list-item' :class="{'menu-list-item-active' : selectedKeys[key] }" :key="key" @click.stop="selectMenuItem({[key]: key})">
          <span :class="{'menu-list-item-text-active': selectedKeys[key]}" >{{ text }}</span>
          <ul v-if="children && children.length > 0 && selectedKeys[key]" class="sub-menu">
            <li v-for="{ key: childKey, text: childText, children: subChildren } in children"  class='menu-list-item'  :key="childKey" @click.stop="selectMenuItem({[key]: key, [childKey]: childKey})">
              <span :class="{'menu-list-item-text-active': selectedKeys[childKey]}" >{{ childText }}</span>
              <ul v-if="subChildren && subChildren.length > 0 && selectedKeys[childKey]" class="sub-menu">
                <li v-for="{ key: subChildKey, text: subChildText } in subChildren"  class='menu-list-item' :class="{'menu-list-item-active' : selectedKeys[subChildKey]}" :key="subChildKey" @click.stop="selectMenuItem({[key]: key, [childKey]: childKey, [subChildKey]: subChildKey})">
                  <span :class="{'menu-list-item-text-active': selectedKeys[subChildKey]}" >{{ subChildText }}</span>
                </li>
              </ul>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<style lang='scss'>
.wrapper {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.nav {
  display: flex;
  justify-content: flex-end;
  color: red;
  padding: 8px;
}

.menu-icon-container {
  border: 1px solid black;
  border-radius: 4px;
  padding: 8px 8px 4px 8px;
}

.container {
  background-color: rgb(224, 224, 224);
  display: flex;
  flex-grow: 1;
  align-items: center;
}

.content {
  width: 100%;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.box {
  width: 33%;
  height: 100px;
  margin-bottom: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.box-content {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
    border: black solid 2px;
  background: radial-gradient(circle, rgba(113,81,95,1) 81%, rgba(0,0,0,1) 100%);
}

.ball {
  width: 30px;
  height: 30px;
  background-color: #A5F12B; 
  border-radius: 50%;
  color: black;
  display: flex;
  justify-content: center;
  align-items: center;
  animation: slideToRight 3s infinite;
  position: relative;
  z-index: 100;
}

.menu {
  width: 200px;
  position: fixed;
  top: 0;
  height: 100vh;
  background-color: black;
  border-right: 1px solid #ccc;
  overflow: hidden;
  transform: translateX(100vw);
  transition: all 0.5s;
}

.show-menu {
  transform: translateX(200px);
}

.menu-list {
  list-style-type: none;
  padding: 4px 0 0 4px;
  color: rgb(225, 225, 225);
}

.menu-list-item {
  cursor: pointer;
  padding: 8px 8px;
}

.menu-list-item-active {
  background-color: rgb(157, 157, 157);
}

.menu-list-item-text-active {
  color: yellow;
}

.sub-menu {
  list-style-type: none;
  padding-left: 20px;
}

.flashing {
  animation: flash 0.6s infinite;
}

@keyframes flash {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.6;
  }
}

@keyframes slideToRight {
  100% {
    transform: translateX(260px);
  }
}

</style>
