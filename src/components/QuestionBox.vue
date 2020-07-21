<template>
    <div class="Question-box-container">
        <b-jumbotron header="Quiz" lead="ss">
            <template v-slot:lead >
                {{ currentQuestion.question == "Email" ? "What is your Email Id ?": currentQuestion.question }}
                <hr class="my-4">
                <b-list-group>
                    <div v-if="currentQuestion.question == 'Email'">
                        <b-input type="email"
                                 v-model="email"
                                 :state="isValidEmail()"
                                 placeholder="Email Id"
                                 required></b-input>
                        <div>
                            <b-button variant="primary"
                                      class="submit-button"
                                      @click="submitEmail(email)"
                                      :disabled="!isValidEmail()"
                            >
                                Submit
                            </b-button>
                        </div>
                    </div>
                    <div v-else>
                        <b-list-group-item v-for="answer in answers"
                                           :key="answer['answer']"
                                           @click="selectedAnswer(answer)"
                                           :class="selectedOption == answer? 'selected': ''"
                        >
                            {{ answer['answer']}}
                        </b-list-group-item>
                        <div>
                            <b-button variant="primary" class="next-button" @click="nextQuestion()">{{processButton}}
                            </b-button>
                        </div>
                    </div>
                </b-list-group>
            </template>

        </b-jumbotron>
    </div>
</template>

<script>
    export default {
        name: "QuestionBox",
        props: {
            currentQuestion: Object
        },
        data() {
            return {
                result: 0,
                selectedOption: [],
                email: "",
                emailReg: /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/,
                processButton: "Next"
            }
        },
        methods: {
            selectedAnswer(answer) {

                this.selectedOption = answer
                if (!this.selectedOption.nextQuestion) {
                    this.processButton = "Submit";
                }
                console.log("selected option", this.selectedOption)
            },
            nextQuestion() {

                if (Object.keys(this.selectedOption).length) {
                    this.$emit('next-question', this.selectedOption)
                }
                console.log(typeof this.selectedOption)
            },
            submitEmail(email) {

                if (this.emailReg.test(email)) {
                    this.isValidEmail = true
                    this.$emit('submit-email', email)
                } else {
                    return false
                }

            },
            isValidEmail() {
                if (this.emailReg.test(this.email)) {
                    return true
                } else {
                    return false
                }
            }
        },
        computed: {
            answers() {
                return this.currentQuestion ? this.currentQuestion.option : [];
            }
        }
    }
</script>

<style scoped>
    .list-group {
        margin-bottom: 10px;
    }

    .submit-button, .next-button {
        margin: 10px;
    }

    .list-group-item:hover {
        background: #EEE;
        cursor: pointer;
    }

    .selected {
        background: #289fff;
    }

</style>