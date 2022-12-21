<template>
    <div class="message-modal flex center just-center">
        <div class="message-modal-content">
            <p class="message-text">
              <slot></slot>
            </p>
        </div>
    </div>
</template>

<script>
export default {
name: 'MessageModal'
}
</script>

<style>
/*==== MODAL MESSAGE ==============*/
.message-modal {
  position: fixed;
  z-index: 33;
  opacity: 1;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,.7);
  backdrop-filter: blur(12px);
  transition: .3s;
}
/* .message-modal.active {
  z-index: 33;
  opacity: 1;
} */
.message-modal-content {
  width: 90%;
  max-width: 500px;
  background-color: #181818;
  color: #fff;
  position: relative;
  font-size: 1.4rem;
  line-height: 1.4;
  padding: 2.5rem 1.5rem;
  border-radius: 8px;
  text-align: center;
}
.message-text {
  margin-bottom: 2rem;
}
.close-img {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 1.2rem;
  height: 1.2rem;
  cursor: pointer;
}
</style>