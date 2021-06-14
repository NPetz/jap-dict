<template>
  <li class="hit">
    <p><slot></slot></p>
  </li>
</template>

<script>
export default { name: "ErrorCell" };
</script>

<style>
.hit {
  display: flex;
  position: relative;
  align-items: center;
  justify-content: center;
  flex: 1 0 auto;
  font-size: 1em;
  padding: 0.5rem 1rem;
  color: #333;
  background-color: #fff;
  border: 1px solid #333;
  white-space: nowrap;
  min-width: 0px;
  min-height: 50px;
  user-select: none;
}
</style>