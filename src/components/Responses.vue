<template>
    <div class="response-btn-container">
        <div id="strongly-agree-btn" class="btn" @click="this.$emit('respond', 1)">Strongly Agree</div>
        <div id="agree-btn" class="btn" @click="this.$emit('respond', 0.5)">Agree</div>
        <div id="neutral-btn" class="btn" @click="this.$emit('respond', 0)">Neutral/Unsure</div>
        <div id="disagree-btn" class="btn" @click="this.$emit('respond', -0.5)">Disagree</div>
        <div id="strongly-disagree-btn" class="btn" @click="this.$emit('respond', -1)">Strongly Disagree</div>
    </div>
</template>

<style>
.response-btn-container {
    display: block;
}

/* use [id=idname] instead of #idname so class selectors weigh more */
.btn[id=strongly-agree-btn] {
    background-color: #b8bb26;
}

.btn[id=agree-btn] {
    background-color: #D2CB6C;
}

.btn[id=neutral-btn] {
    background-color: #ebdbb2;
}

.btn[id=disagree-btn] {
    background-color: #F39273;
}

.btn[id=strongly-disagree-btn] {
    background-color: #fb4934;
}
</style>