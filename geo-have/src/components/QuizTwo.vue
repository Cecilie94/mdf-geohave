<template>
    <main class="app">
        <h1>Quiz!</h1>
        <span class="score">
            {{ score }}/{{ currentQuestions.length }}
        </span>
        <section class="quiz" v-if="!quizCompleted">
            <div class="quiz-info">
                <span class="question">
                    {{ getCurrentQuestion.question }}
                </span>
            </div>

            <div class="options">
                <label v-for="(option, index) in getCurrentQuestion.options" 
                    :key="index"
                    :class="`option ${
                        getCurrentQuestion.selected == index
                        ? index == getCurrentQuestion.answer
                            ? 'correct' : 'wrong' : ''
                    }${
                        getCurrentQuestion.selected != null &&
                        index != getCurrentQuestion.selected
                            ? 'disabled'
                            : ''
                    }`">
                    <input type="radio" 
                        :name="getCurrentQuestion.index"
                        :value="index"
                        v-model="getCurrentQuestion.selected"
                        :disabled="getCurrentQuestion.selected"
                        @change="setAnswer">
                    <span>{{ option }}</span>
                </label>
            </div>
            <button 
                v-show="getCurrentQuestion.selected !== null || quizCompleted.value"
                @click="finishQuiz"
                :disabled="!getCurrentQuestion.selected">
                {{ 
                    getCurrentQuestion.index == currentQuestions.length - 1
                    ? 'Færdig'
                    : getCurrentQuestion.selected == null 
                        ? 'Select an option'
                        : 'Next question'
                }}
            </button>
        </section>

        <section v-else>
            <h2>You have finished the quiz!</h2>
            <p>Your score is {{ score }}/{{ currentQuestions.length }}</p>
            <router-link to="/pointshop">
                <button>Gå til shop</button>
            </router-link>
        </section>
    </main>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

const questions = [
    {
        question: "Hvilken slags blomst er Solbrud?",
        answer: 3,
        options: [
            'En rose',
            'En tulipan',
            'En nellike',
            'En solsikke' //Correct answer
        ], 
        selected: null 
    },
];

let currentQuestions = ref(questions);
const quizCompleted = ref(false);
const currentQuestion = ref(0);

const score = computed(() => {
    let value = 0;
    currentQuestions.value.forEach(q => {
        if (q.selected === q.answer) {
            value++;
        }
    });
    return value;
});

const getCurrentQuestion = computed (() => {
    let question = currentQuestions.value[currentQuestion.value];
    question.index = currentQuestion.value;
    return question;
});

const setAnswer = (e) => {
    currentQuestions.value[currentQuestion.value].selected = parseInt(e.target.value);
};

const nextQuestion = () => {
    if (currentQuestion.value < currentQuestions.value.length - 2) {
        currentQuestion.value++;
    } else {
        quizCompleted.value = true;
    }
};

const finishQuiz = () => {
    const isCorrect = getCurrentQuestion.value.selected === getCurrentQuestion.value.answer;
    console.log("Is correct:", isCorrect);
    if (isCorrect) {
        router.push('/quiz/points');  // Navigate to PointsView.vue
    } else {
        console.log("Navigating to /NotCorrect");
        router.push('/NotCorrect'); // Navigate to NotCorrect.vue
    }
};


</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300..800;1,300..800&display=swap');

@font-face {
  font-family: "Stag";
  src: url("../assets/fonts/stag_regular.ttf") format("truetype");
}

.app {
    display: flex;
    flex-direction: column;
    padding: 2rem;
    height: 100vh;
    margin-top: 60px;
}

h1 {
    font-family: "Stag";
    font-weight: 900;
    font-size: 36px;
    margin-bottom: 10px;
}

.quiz {
    width: 100%;
    max-width: 650px;
}

.quiz-info{
    display: flex;
    justify-content: space-between;
    margin-bottom: 1rem;
}

.quiz-info .question{
    color: #090909;
    font-family: "Open Sans", sans-serif;
    font-weight: 300;
    font-size: 24px;
}

.quiz-info .score {
    color: #fff;
    font-size: 1.25rem;
}

input[type="radio"] {
    display: none;
}

.options{
    margin-bottom: 1rem;
    font-size: 24px; 
     margin-top: 2rem;
}

.option{
    padding: 1rem;
    display: block;
    background-color: #f1f1f1;
    margin-bottom: 15px;
    border-radius: 0.5rem;
    cursor: pointer ;
    font-family: "Open Sans", sans-serif;
    font-weight: 700;
    text-align: center;
    box-shadow: -3px 6px 10px rgba(0, 0, 0, 0.3);
    user-select: none;
}

span {
    font-family: "Open Sans", sans-serif;
    font-weight: 700;
    font-size: 24px;
}

.option input[type="radio"]:checked + span {
    color: white; 
}

.option.correct{
    background-color: #87A669;
}

.option.wrong{
    background-color: #CC807D;
}

.option:last-of-type{
    margin-bottom: 0;
}

.option.disabled{
    opacity: 0.5;
}

option.input {
    display: none;
}

button {
    appearance: none;
    outline: none;
    border: none;
    cursor: pointer;
    margin: 0 89px; 
    margin-top: 15px;
    background-color: #f1f1f1;
    color: #000;
    font-size: 24px;
    font-family: "Open Sans", sans-serif;
    font-weight: 700;
    width: 189px;
    height: 55px;
    box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1); /* Tilføj skyggeeffekt */
}

button:disabled {
    width: 189px;
    height: 55px;
    text-align: center; 
    box-shadow: none; 
}

h2 {
    font-size: 2rem;
    margin-bottom: 2rem;
    text-align: center;

}

p {
    color: #000;
    font-size: 1rem;
    text-align: center;
}

</style>