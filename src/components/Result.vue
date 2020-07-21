<template>
    <div>
        <b-jumbotron header="Result" >
            <p> Your hair scored {{score}}</p>
            <b-button variant="primary" href="/">Close</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    export default {
        name: "Result",
        props: { score: Number }
    }
</script>

<style scoped>

</style>