<template>
    <div class="question-container">
        <div class="question-counter header">Question {{ questionIndex + 1 }} of {{ maxQuestions }}</div>
        <div class="question-content">{{ currentQuestion }}</div>
    </div>
</template>

<script>
export default {
    props: {
        questionIndex: Number,
        maxQuestions: Number,
        currentQuestion: String,
    }
}
</script>

<style>
.question-container {
    font-size: 20px;
    text-align: center;
}

.question-counter {
    font-weight: 500;
    font-size: 40px;
    margin: 8px;
}

.question-content {
    margin: 32px 64px 64px 64px;
}
</style>