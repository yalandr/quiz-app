<template>
    <footer class="footer">
        <div class="control-panel">
            <div class="container flex center just-btwn">
                <img src="../assets/img/svg/arrow-back.svg" alt="Arrow back" class="arrow-back-img">
                <div class="btn-wrapper flex center">
                    <MyButton class="btn-next flex center just-center" disabled>
                        Наступне питання 
                        <img src="../assets/img/svg/arrow-next.svg" alt="Arrow next" class="arrow-next-img">
                    </MyButton>
                    <p class="text-secondary">
                        Або натисніть <br>
                        <strong>Enter</strong>
                    </p>
                </div>
                <div class="notification">
                    Ок, натисніть, <br>
                    щоб завершити <br>
                    <img src="../assets/img/png/down-arrow.png" alt="Arrow down" class="arrow-down">
                </div>
            </div>
        </div>
        <div class="progress-wrapper">
            <div class="container flex center">
                <p class="footer-text flex center">
                    Готово:&nbsp;<span class="progress-percentage">0%</span>
                </p>
                <div class="progress-bar">
                    <div class="progress-bar-line"></div>
                </div>
            </div>
        </div>
    </footer>
</template>

<script>
import MyButton from './UI/MyButton.vue';

export default {
    components: { MyButton },
    name: 'Footer'
}
</script>

<style>
/*==== FOOTER =====================*/
.footer {
  width: 100%;
  background-color: transparent;
}
.progress-wrapper {
  border-top: 1px solid #222222;
  padding: 1.5rem 0;
}
.footer-text {
  font-weight: 300;
  font-size: 1rem;
  line-height: 1.5;
  color: #BFBFBF;
  margin-right: 10px;
}
.footer-text span {
  font-weight: 700;
  color: #b8e994;
}
.progress-bar {
  position: relative;
  width: 100%;
  height: 0.5rem;
  background-color: #242424;
  border-radius: 8px;
  overflow: hidden;
}
.progress-bar-line {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 0;
  background: repeating-linear-gradient(
  -55deg,
  #EE9534,
  #EE9534 6px,
  #FBB564 6px,
  #FBB564 12px
  );
  transition: .3s;
}
</style>