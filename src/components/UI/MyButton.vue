<template>
  <button class="btn">
    <slot></slot>
  </button>
</template>

<script>
export default {
    name: 'MyButton'
}
</script>

<style>
.btn {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 14.5rem;
    min-height: 3rem;
    background: #0be881;
    border-radius: 8px;
    font-family: var(--font-primary), sans-serif;
    font-style: normal;
    font-weight: 700;
    font-size: 1rem;
    color: #111111;
    cursor: pointer;
    border: none;
    opacity: 0.5;
    transition: .1s;
}
.btn:active {
    transform: scale(0.99);
}
</style>