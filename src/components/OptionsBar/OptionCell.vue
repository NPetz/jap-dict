<template>
  <li class="hit">
    <p><slot></slot></p>
  </li>
</template>

<script>
export default { name: "OptionCell" };
</script>

<style>
.hit {
  display: flex;
  position: relative;
  align-items: center;
  justify-content: center;
  flex: 1 0 auto;
  font-size: 1rem;
  padding: 0.5rem 1rem;
  color: #333;
  background-color: #fff;
  border: 1px solid #333;
  white-space: nowrap;
  cursor: pointer;
  transition: all 0.2s;
  min-width: 0px;
  min-height: 50px;
  user-select: none;
}

.hit::after {
  position: absolute;
  content: "";
  width: 100%;
  height: 3px;
  bottom: 0;
  left: 0;
  background: #333;
  transition: transform 0.3s;
  transform: scaleX(0);
  transform-origin: right;
}
.hit:hover::after {
  transform: scaleX(1);
  transform-origin: left;
}

.hit.active {
  color: #eee;
  background-color: #333;
  min-width: 95%;
  font-size: 1.2rem;
}
</style>