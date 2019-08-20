<template>
    <div>
        <b-jumbotron>
            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item
                    button
                    v-for="(answer, index) in shuffledAnswers" :key="index"
                    @click="selectAnswer(index)"
                    :class="answerClass(index)"
                    :disabled="answered"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button
                variant="primary"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered"
            >
                Submit</b-button>
            <b-button variant="success" @click="next">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash'

    export default {
        props: {
            currentQuestion: Object,
            next: Function,
            increment: Function
        },
        data() {
            return {
                selectedIndex: null,
                correctIndex: null,
                shuffledAnswers: [],
                answered: false
            }
        },
        computed: {
            answers() {
                let answers = [...this.currentQuestion.incorrect_answers];
                answers.push(this.currentQuestion.correct_answer);
                return answers;
            }
        },
        watch: {
          currentQuestion: {
              immediate: true,
              handler() {
                  this.answered = false;
                  this.selectedIndex = null;
                  this.shuffleAnswers()
              }
          }
        },
        methods: {
            selectAnswer(index) {
                this.selectedIndex = index;
            },
            submitAnswer() {
              let isCorrect = false;
              this.answered = true;

                if(this.selectedIndex === this.correctIndex) {
                    isCorrect = true;
                }

                this.increment(isCorrect)
            },
            shuffleAnswers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                this.shuffledAnswers = _.shuffle(answers);
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
            },
            answerClass(index) {
                let answerClass = '';

                if(!this.answered && this.selectedIndex === index) {
                    answerClass = 'selected';
                } else if(this.answered && this.correctIndex === index ) {
                    answerClass = 'correct';
                } else if(this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answerClass = 'incorrect';
                }

                return answerClass;
            }
        }
    }
</script>

<style scoped>
    .list-group {
        margin-bottom: 16px;
    }

    .btn {
        margin: 0 4px;
    }

    .list-group-item.selected {
        background-color: lightblue;
    }

    .list-group-item.correct {
        background-color: lightgreen;
    }

    .list-group-item.incorrect {
        background-color: red;
    }
</style>