<template>
    <div id="app">
        <!--    <img alt="Vue logo" src="./assets/logo.png">-->
        <Header></Header>
        <b-container class="bv-example-row">
            <b-row>
                <b-col sm="6" offset="3">
                    <QuestionBox
                            v-show = "!quizEnded"
                            v-if="currentQuestion"
                            :currentQuestion="currentQuestion"
                            @next-question="next"
                            @submit-email="submitEmail"
                    ></QuestionBox>
                    <Result v-show="quizEnded" :score="result"></Result>
                </b-col>
            </b-row>
        </b-container>
    </div>
</template>

<script>
    // import HelloWorld from './components/HelloWorld.vue'
    import QuestionBox from "./components/QuestionBox";
    import Header from "./components/Header";
    import Result from "./components/Result";

    export default {
        name: 'App',
        components: {
            Result,
            Header,
            QuestionBox,
        },
        data() {
            return {
                hasQuestion: true,
                question: [],
                currentQuestion: {},
                result: 0,
                quizEnded: false,
                dataToPost: {'user_answers': [], 'email': ""}
            }
        },
        methods: {
            next(selectedOption) {
                let id = this.currentQuestion['question_id']
                if (selectedOption.nextQuestion) {
                    this.result += selectedOption['point'];
                    // let id = this.currentQuestion['question_id']
                    this.dataToPost.user_answers.push({'question_id': id, 'answer':selectedOption["answer"]})
                    this.currentQuestion = this.question[selectedOption.nextQuestion]
                } else {
                    this.result += selectedOption['point'];
                    this.dataToPost.user_answers.push({'question_id': id, 'answer':selectedOption["answer"]})
                    this.quizEnded = true;
                    this.submitQuiz();
                }
            },
            submitEmail(email) {
                this.currentQuestion = this.question[this.currentQuestion.option.startedQuestion]
                this.dataToPost['email'] = email
            },
            submitQuiz() {
                console.log(JSON.stringify(this.dataToPost))
                fetch('http://192.168.31.222:1234/bareanatomy/?action=submitAnswer', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json;charset=utf-8'
                    },
                    body: JSON.stringify(this.dataToPost)
                })

            }
        },
        mounted: function () {

            fetch('http://192.168.31.222:1234/bareanatomy/?action=getQuestion', {method: 'GET'})
                .then((response) => {
                    return response.json()
                }).then((jsonData) => {

                    let emailQuestion = {"question": "Email", "option": {"startedQuestion": jsonData['startQuestion']}}
                    this.question = {...jsonData['questions']}
                    this.currentQuestion = emailQuestion
            })
        }

    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
