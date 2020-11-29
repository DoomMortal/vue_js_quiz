<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template #lead>
                {{currentQuestion.question}}
            </template>

            <hr class="my-4" />

            <b-list-group>
                <b-list-group-item
                v-for="(answer, index) in answers"
                v-bind:key="answer.id"
                @click="selectedAnswer(index)"
                :class="answerClass(index)">
                    {{answer}}
                </b-list-group-item>
            </b-list-group>

            <b-button variant="primary"
            @click="submitAnswer"
            :disabled="selectedIndex === null || answered">
                Sumbit
            </b-button>
            <b-button @click="next" variant="success">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
export default {
    name: "QuestionBox",
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data(){
        return {
            selectedIndex: null,
            correctIndex: null,
            answered: false
        }
    },
    computed: {
        answers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
            this.shuffleArray(answers)
            return answers;
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler(){
                this.selectedIndex = null
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                this.answered = false;
                this.shuffleArray(answers);
                this.correctIndex = this.answers.indexOf(this.currentQuestion.correct_answer)
                return answers;
            }
            
        }
    },
    methods: {
        shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        },
        selectedAnswer(index){
            this.selectedIndex = index;
        },
        submitAnswer(){
            let isCorrect = false
            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true;

            this.increment(isCorrect);
        },
        answerClass(index){
            let answerClass = "";

            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selected';
            }else if(this.answered && this.correctIndex === index){
                answerClass = 'correct';
            }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrect';
            }

            return answerClass;
        }
    }
}
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }
    .list-group-item:hover {
        background-color: #EEE;
        cursor: pointer;
    }
    .selected {
        background-color: lightblue;
    }
    .correct {
        background-color: lightgreen;
    }
    .incorrect {
        background-color: red;
    }
    .btn {
        margin: 0 5px;
    }
</style>