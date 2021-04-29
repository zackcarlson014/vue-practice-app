<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template slot="lead">
                {{ formatedQuestion }}
            </template>

            <hr class="my-4" />

            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in shuffledAnswers" 
                    :key="index"
                    @click="selectAnswer(index)"
                    :class="answeredClass(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>
    
            <b-button 
                variant="primary" 
                href="#"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered"
            >
                Submit
            </b-button>
            <b-button 
                @click="next" 
                variant="success" 
                href="#"
                :disabled="!answered"
            >
                Next
            </b-button>

        </b-jumbotron>
    </div>
</template>

<script>

    import _ from 'lodash';

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
                answered: false,
                formatedQuestion: ''
            }
        },
        computed: {
            answers() {
                let answers = [...this.currentQuestion.incorrect_answers]
                answers.push(this.currentQuestion.correct_answer)
                return answers
            }
        },
        watch: {
            currentQuestion: {
                immediate: true,
                handler() {
                    this.selectedIndex = null
                    this.answered = false
                    this.shuffleAnswers()
                    this.reformatQuestion()
                }    
            }
        },
        methods: {
            reformatQuestion() {
                let question = this.currentQuestion.question.replaceAll("&#039;", "'").replaceAll("&quot;", "\"")
                this.formatedQuestion = question
            },
            shuffleAnswers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
                let shuffledAnswers = _.shuffle(answers).map(answer => {
                    return answer.replaceAll("&#039;", "'").replaceAll("&quot;", "\"")
                })
                this.shuffledAnswers = shuffledAnswers
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            },
            selectAnswer(index) {
                this.selectedIndex = index
            },
            submitAnswer() {
                let isCorrect = false
                if (this.selectedIndex === this.correctIndex) {
                    isCorrect = true
                }
                this.answered = true
                this.increment(isCorrect)
            },
            answeredClass(index) {
                let answerClass = ''

                if (!this.answered && this.selectedIndex === index) {
                    answerClass = 'selected'
                } else if (this.answered && this.correctIndex === index) {
                    answerClass = 'correct'
                } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answerClass = 'incorrect'
                }

                return answerClass
            }  
        }
    }
</script>

<style scoped>
    .list-group {
        margin-bottom: 25px;
    }

    .list-group-item:hover {
        background: #EEE;
        cursor: pointer;
    }

    .btn {
        margin: 0 5px;
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
</style>