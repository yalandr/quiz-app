<template>
<div class="main-wrapper flex column">
    <Header />
        <main class="main">
            <form 
                class="main-form" 
                id="mainForm" 
                @change="dataRecording"
            >
                <div 
                    v-for="quizStep in quizSteps"
                    v-show="quizStep.isActive" 
                    :key="quizStep.number" 
                    :class="quizStep.class"
                >
                    <div class="question-wrapper">
                        <div class="container flex center">
                            <div class="question-number">
                                {{ quizStep.number }}.
                            </div>
                            <h3 class="question-name">
                                {{ quizStep.question }}
                            </h3>
                            <p class="text-secondary" v-if="quizStep.isMultiple">
                                *(Доступний множинний вибір)
                            </p>
                        </div>
                    </div>
                    <div class="answer-wrapper">
                        <div class="container">
                            <p class="text-secondary list-message" v-if="quizStep.isListMessageVisible">
                                Тут буде перелік рекламодавців ...
                            </p>
                            <ul class="added-items-list" v-if="quizStep.isList">
                                <li 
                                    class="added-list-item flex center just-btwn"
                                    v-for="addedItem in addedItemsList"
                                    :key="addedItem.id"
                                    :id="addedItem.id"
                                >
                                    {{ addedItem.value }}
                                    <button 
                                        class="delete-btn flex center just-center" 
                                        title="Delete"
                                        @click.prevent="deleteAddedItem"
                                    ></button>
                                </li>
                            </ul>
                            <div :class="quizStep.inputWrapperClass">
                                <div v-for="input in quizStep.inputs" :key="input.id" :class="input.groupClass">
                                    <input 
                                        :type="input.type" 
                                        :name="input.name" 
                                        :placeholder="input.placeholder" 
                                        :class="input.inputClass" 
                                        :id="input.id" 
                                        :value="input.value"
                                        :required="input.isRequired"
                                        :checked="input.checked"
                                        @click="inputClickHandler"
                                        @input="inputChanging"
                                    >
                                    <label :for="input.id">
                                        {{ input.value }}
                                    </label>
                                    <button 
                                        v-if="quizStep.isList" 
                                        class="add-btn" 
                                        title="Додати"
                                        @click.prevent="addBtnClick"
                                        :disabled="isAddBtnDisabled"
                                    ></button>
                                </div>
                            </div>
                            <div class="addition-input-wrapper" v-if="isChecked">
                                <h4 class="addition-input-heading">
                                    Ваш логін
                                </h4>
                                <div class="form-group">
                                    <input 
                                        type="text" 
                                        name="Login" 
                                        placeholder="Введіть ваш логін" 
                                        class="form-control text" 
                                        id="el-0205" 
                                        required
                                        @input="inputChanging"
                                    >
                                    <label for="el-0205"></label>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </form>
        </main>
        <footer class="footer">
            <div class="control-panel">
                <div class="container flex center just-btwn">
                    <p v-if="isNotTheFirstQuestion" @click="switchToPreviousQuestion">
                        back
                    </p>
                    <!-- <img src="assets/img/svg/arrow-back.svg" alt="Arrow back" class="arrow-back-img"> -->
                    <div class="btn-wrapper flex center">
                        <button 
                            class="btn btn-next flex center just-center" 
                            :disabled="isButtonNextDisabled"
                            @click="switchToNextQuestion"
                        >
                            Наступне питання 
                            <!-- <img src="assets/img/svg/arrow-next.svg" alt="Arrow next" class="arrow-next-img"> -->
                        </button>
                        <p class="text-secondary">
                            Або натисніть <br>
                            <strong>Enter</strong>
                        </p>
                    </div>
                    <div class="notification">
                        Ок, натисніть, <br>
                        щоб завершити <br>
                        <!-- <img src="assets/img/png/down-arrow.png" alt="Arrow down" class="arrow-down"> -->
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
        <MessageModal v-if="isModalActive"></MessageModal>
    </div>
</template>

<script>
import Header from './components/Header.vue';
import MessageModal from './components/UI/MessageModal.vue';

export default {
  components: {
    Header,
    MessageModal,
  },
  data() {
    return {
        isChecked: false,
        isModalActive: false,
        isButtonNextDisabled: true,
        currentQuestion: 0,
        isNotTheFirstQuestion: false,
        trafficCheckboxesChecked: [],
        verticalCheckboxesChecked: [],
        question: '',
        answer: '',
        quizObject: {},
        isAddBtnDisabled: true,
        addedItemsList: [],
        addedItemsValuesList: [],
        quizSteps: [
            {
                number: 1,
                question: "Вкажіть ваше ім’я або нікнейм",
                class: "quiz-step flex column",
                inputWrapperClass: "null",
                isMultiple: false,
                isActive: true,
                inputs: [
                    {
                        id: 'el-0101',
                        type: 'text',
                        name: 'Name',
                        placeholder: "Введіть ваше ім’я або нікнейм",
                        groupClass: "form-group",
                        inputClass: "form-control text",
                        isRequired: true,
                        value: null,
                        checked: this.isChecked
                    }
                ]
            },
            {
                number: 2,
                question: "Вкажіть зручний метод зв'язку",
                class: "quiz-step flex column",
                inputWrapperClass: "form-group-wrapper flex wrap just-btwn",
                isMultiple: false,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0201',
                        type: 'radio',
                        name: 'Connection',
                        placeholder: null,
                        groupClass: "form-group radio",
                        inputClass: "form-control radio",
                        isRequired: false,
                        value: "Skype",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0202',
                        type: 'radio',
                        name: 'Connection',
                        placeholder: null,
                        groupClass: "form-group radio",
                        inputClass: "form-control radio",
                        isRequired: false,
                        value: "Telegram",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0203',
                        type: 'radio',
                        name: 'Connection',
                        placeholder: null,
                        groupClass: "form-group radio",
                        inputClass: "form-control radio",
                        isRequired: false,
                        value: "WeChat",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0204',
                        type: 'radio',
                        name: 'Connection',
                        placeholder: null,
                        groupClass: "form-group radio",
                        inputClass: "form-control radio",
                        isRequired: false,
                        value: "WhatsApp",
                        checked: this.isChecked
                    },
                ]
            },
            {
                number: 3,
                question: "Вкажіть ваш email",
                class: "quiz-step flex column",
                inputWrapperClass: "null",
                isMultiple: false,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0301',
                        type: 'email',
                        name: 'Email',
                        placeholder: "Введіть ваш email",
                        groupClass: "form-group",
                        inputClass: "form-control email",
                        isRequired: true,
                        value: null,
                        checked: this.isChecked
                    }
                ]
            },
            {
                number: 4,
                question: "Вкажіть ваше джерело трафіку",
                class: "quiz-step flex column",
                inputWrapperClass: "form-group-wrapper flex wrap just-btwn",
                isMultiple: true,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0401',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Facebook",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0402',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Google",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0403',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Native (Teaser)",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0404',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Email",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0405',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Push",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0406',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Pop",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0407',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "In-app",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0408',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "TikTok",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0409',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Telegram",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0410',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "SMS",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0411',
                        type: 'checkbox',
                        name: 'Traffic',
                        placeholder: null,
                        groupClass: "form-group checkbox w-100",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Інше джерело трафіку",
                        checked: this.isChecked
                    },
                ]
            },
            {
                number: 5,
                question: "З якою вертикаллю працюєте?",
                class: "quiz-step flex column",
                inputWrapperClass: "form-group-wrapper flex wrap just-btwn",
                isMultiple: true,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0501',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Forex / Crypto",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0502',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Gambling / Betting",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0503',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Adult / Dating",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0504',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "NFT",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0505',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "COD",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0506',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Insurance",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0507',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Gaming",
                        checked: this.isChecked
                    },
                    {
                        id: 'el-0508',
                        type: 'checkbox',
                        name: 'Vertical',
                        placeholder: null,
                        groupClass: "form-group checkbox",
                        inputClass: "form-control checkbox",
                        isRequired: false,
                        value: "Other",
                        checked: this.isChecked
                    },
                ]
            },
            {
                number: 6,
                question: "Вкажіть рекламодавців, з якими працюєте",
                class: "quiz-step flex column",
                inputWrapperClass: "null",
                isMultiple: false,
                isList: true,
                isListMessageVisible: true,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0601',
                        type: 'text',
                        name: 'Advertiser',
                        placeholder: "Введіть назви рекламодавців",
                        groupClass: "form-group",
                        inputClass: "form-control adv-input",
                        isRequired: false,
                        value: null,
                        checked: this.isChecked
                    }
                ],
            },
            {
                number: 7,
                question: "Вкажіть кількість баєрів у команді",
                class: "quiz-step flex column",
                inputWrapperClass: "null",
                isMultiple: false,
                isList: false,
                isActive: false,
                inputs: [
                    {
                        id: 'el-0701',
                        type: 'number',
                        name: 'Team',
                        placeholder: "Введіть кількість баєрів",
                        groupClass: "form-group",
                        inputClass: "form-control text number",
                        isRequired: false,
                        value: null,
                        checked: this.isChecked
                    }
                ]
            },
            {
                number: null,
                question: "Results",
                class: "quiz-step flex column",
                inputWrapperClass: "null",
                isMultiple: false,
                isList: false,
                isActive: false,
                inputs: []
            },
        ],
    }
  },
  methods: {
    btnAble() {
        this.isButtonNextDisabled = false;
        this.$el.querySelector('.btn.btn-next').style.opacity = '1';
    },
    btnDisable() {
        this.isButtonNextDisabled = true;
        this.$el.querySelector('.btn.btn-next').style.opacity = '0.5';
    },
    inputChanging(e) {
        let currentInput = e.target;

        console.log(currentInput.value);

        if (currentInput.name === "Name" || currentInput.name === "Login") {
            if (currentInput.value.trim() !== '' && currentInput.value.trim() !== ' ') {
                // this.btnAble();
                currentInput.classList.add('valid');
                this.$el.querySelector('.btn.btn-next').style.opacity = '1';
            } else {
                // this.btnDisable();
                currentInput.classList.remove('valid');
            }
        }

        if (currentInput.name === "Email") {
            if (currentInput.value.trim() !== '' && currentInput.value.trim() !== ' ') {
                this.btnAble();
                currentInput.classList.add('valid');
            } else {
                this.btnDisable();
                currentInput.classList.remove('valid');
            }
        }

        if (currentInput.type === "checkbox") {
            if (currentInput.checked) {
                this.btnAble();
                if (currentInput.name === "Traffic") {
                    this.trafficCheckboxesChecked.push(currentInput.value);
                } else if (currentInput.name === "Vertical") {
                    this.verticalCheckboxesChecked.push(currentInput.value);
                }
            } else if (this.trafficCheckboxesChecked.length || this.verticalCheckboxesChecked.length) {
                if (currentInput.name === "Traffic") {
                    this.trafficCheckboxesChecked.splice(this.trafficCheckboxesChecked.indexOf(currentInput.value), 1);
                    if (!this.trafficCheckboxesChecked.length) {
                        this.btnDisable();
                    }
                } else if (currentInput.name === "Vertical") {
                    this.verticalCheckboxesChecked.splice(this.verticalCheckboxesChecked.indexOf(currentInput.value), 1);
                    if (!this.verticalCheckboxesChecked.length) {
                        this.btnDisable();
                    }
                } 
            } 
        }

        if (currentInput.name === 'Advertiser') {
            if (currentInput.value.trim()) {
                this.isAddBtnDisabled = false;
            } else {
                this.isAddBtnDisabled = true;
            }
        }

        if (currentInput.name === 'Team') {
            if (currentInput.value.trim()) {
                this.btnAble();
            } else {
                this.btnDisable();
            }
        }
    },
    inputClickHandler(e) {
        if (e.target.type == "radio") {
            this.isChecked = true;
        } 
        // else if (e.target.type == "email") {
        //     console.log('email');
        // } 
        else if (e.target.type == "checkbox") {
            this.btnAble();
        } 
        // else if (e.target.type == "number") {
        //     console.log('number');
        // }
    },
    switchToNextQuestion() {
        if (this.quizSteps[this.currentQuestion].number < this.quizSteps.length) {
            this.quizSteps[this.currentQuestion].isActive = false;
            this.quizSteps[this.currentQuestion + 1].isActive = true;
            this.btnDisable();
            this.currentQuestion += 1;
            this.isChecked = false;
            this.isInputValidated = false;
            this.isNotTheFirstQuestion = true;
            this.progressBarUpdate();
        } else {
            console.log('no switch!')
        }
    },
    switchToPreviousQuestion() {
        if (this.quizSteps[this.currentQuestion].number > 1) {
            this.quizSteps[this.currentQuestion].isActive = false;
            this.quizSteps[this.currentQuestion - 1].isActive = true;
            this.btnDisable();
            this.currentQuestion -= 1;
            this.isChecked = false;
            if (this.quizSteps[this.currentQuestion].number === 1) {
                this.isNotTheFirstQuestion = false;
            }
            this.progressBarUpdate();
        } else {
            return false;
        }
    },
    listMessageHiding() {
        this.quizSteps[this.currentQuestion].isListMessageVisible = false;
    },
    listMessageShowing() {
        this.quizSteps[this.currentQuestion].isListMessageVisible = true;
    },
    addBtnClick() {
        if (this.$el.querySelector('.adv-input').value) {
            this.addedItemsList.push(
                {
                    id: this.$el.querySelector('.adv-input').value,
                    value: this.$el.querySelector('.adv-input').value
                }
            ); 

            this.addedItemsValuesList.push(this.$el.querySelector('.adv-input').value);
            
            this.isInputValidated = true;
            this.listMessageHiding();
            this.btnAble();
        } else {
            return false;
        }
    },
    deleteAddedItem(e) {

        e.target.parentElement.remove();
        let itemToDelete = this.addedItemsValuesList.indexOf(e.target.parentElement.id);
        this.addedItemsList.splice(itemToDelete, 1);
        this.addedItemsValuesList.splice(itemToDelete, 1);

        if (this.addedItemsValuesList.length === 0) {
            this.btnDisable();
            this.listMessageShowing();
        }

        console.table(this.quizObject);
    },
    dataRecording(event) {
        this.question = event.target.name;
        this.answer = event.target.value.trim();
        this.quizObject[this.question] = this.answer;

        if (event.target.name === "Traffic") {
            this.quizObject[this.question] = this.trafficCheckboxesChecked; 
        }

        if (event.target.name === "Vertical") {
            this.quizObject[this.question] = this.verticalCheckboxesChecked; 
        }

        if (event.target.name === "Advertiser") {
            this.quizObject[this.question] = this.addedItemsValuesList; 
        }

        console.table(this.quizObject);
    },
    progressBarUpdate() {
        this.$el.querySelector('.progress-bar-line').style.width = 100 / this.quizSteps.length * this.currentQuestion + '%';
        this.$el.querySelector('.progress-percentage').innerText = Math.ceil(100 / this.quizSteps.length * this.currentQuestion) + '%';
    }
  }
}
</script>


<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
/*==== VARIABLES ===============*/
:root {
    --main-bg-color: #111111;
    --main-text-color: #fff;
    --font-primary: 'Nexa';
}
/*==== COMMON =================*/
html {
    overflow-x: hidden;
    scroll-behavior: smooth;
}
body {
    min-height: 100vh;
    overflow-x: hidden;
    font-family: var(--font-primary), 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-size: 1rem;
    background-color: var(--main-bg-color);
    color: var(--main-text-color);
}
a {
    color: inherit;
    text-decoration: none;
}
.container {
    width: 90%;
    max-width: 1280px;
    margin: 0 auto;
}
.flex {
    display: flex;
}
.wrap {
    flex-wrap: wrap;
}
.no-wrap {
    flex-wrap: nowrap !important;
}
.column {
    flex-direction: column;
}
.center {
    align-items: center;
}
.just-btwn {
    justify-content: space-between;
}
.just-center {
    justify-content: center;
}
.flex-end {
    justify-content: flex-end;
}
.main-wrapper {
  min-height: 100vh;
}
.main {
  flex: 1;
}
</style>
