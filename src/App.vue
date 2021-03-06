<template>
    <div>
        <div v-if="currentPage == 'home'">
            <h1 class="center">{{ info.name }}</h1>
            <div style="text-align: center;">
                <span style="margin-right: 8px;">{{ info.author }}</span>
                <a :href="'https://gist.githubusercontent.com/' + infoSource" class="mute">view source</a>
            </div>
            <div class="line"></div>
            <div>{{ info.description }}</div>
            <div v-if="info.name == undefined">
                No source found. Append the URL with ?id=(github gist ID) to load a test.
                <a href="https://humanoidsandvichdispenser.github.io/vuevalues/?id=54094bd6b5432660dabd06bb5549c31f" class="mute">Click here</a> to view a sample test.
            </div>
            <div v-if="info.name != undefined" class="btn accent" @click="this.reset(); currentPage = 'answer'">Start Test</div>
            <br>
        </div>
        <div v-if="currentPage == 'answer'">
            <Question v-if="questionIndex < info.questions.length" :questionIndex="questionIndex" :maxQuestions="info.questions.length" :currentQuestion="info.questions[questionIndex].question" />
            <Responses @respond="addScore($event); nextQuestion();"/>
        </div>
        <div v-if="currentPage == 'results'">
            <div class="header center">Results</div>
            <Progress v-for="(percentage, index) in scorePercentages" :key="index" :leftColor="info.values[index].valueColor" :rightColor="info.values[index].converseColor"
                :progress="Math.round(percentage * 100)" :leftLabel="info.values[index].name" :rightLabel="info.values[index].converse"/>
            <div class="line"></div>
            <span v-if="true">Your closest ideology is: <span style="#8ec07c">{{ calculateIdeology(scorePercentages) }}</span></span>
            <div class="btn accent" @click="currentPage = 'home'">Back to Home</div>
        </div>
    </div>
</template>

<script>
import Question from "./components/Question.vue";
import Responses from "./components/Responses.vue";
import Progress from "./components/ProgressBar.vue";

export default {
    name: "App",
    components: {
        Question,
        Responses,
        Progress
    },
    data() {
        return {
            questionIndex: 0,
            infoSource: "",
            info: {},
            scores: [],
            scorePercentages: [],
            currentPage: "home",
        }
    },
    methods: {
        calculateMaxScore: function(valueIndex) {
            let sum = 0;
            this.info.questions.forEach(question => {
                if (question.effects[valueIndex] != undefined) {
                    if (question.effects[valueIndex] > 0) {
                        sum += question.effects[valueIndex];
                    } else {
                        sum += -question.effects[valueIndex];
                    }
                }
            });
            return sum;
        },
        calculatePercentageScores: function() {
            const percentageScores = [];

            this.scores.forEach((score, i) => {
                percentageScores[i] = ((score + this.calculateMaxScore(i)) / (this.calculateMaxScore(i) * 2)) ?? 0.5; // return 50% (balanced) if could not calculate max score
            })

            return percentageScores;
        },
        calculateIdeology: function() {
            let closestDistance = -1;
            let closestIdeology = undefined;

            console.log(this.info);

            Object.keys(this.info.ideologies).forEach((key) => {
                let ideology = this.info.ideologies[key];
                let magnitude = [];

                ideology.forEach((coordinate, index) => {
                    magnitude[index] = coordinate - this.scorePercentages[index] * 100;
                });

                const distance = Math.sqrt(magnitude.reduce((a, b) => a + Math.pow(b, 2), 0)); // sum of all elements squared

                console.log(`Distance from ${key}: ${distance}`);

                if (distance < closestDistance || closestDistance == -1) {
                    closestDistance = distance;
                    closestIdeology = key;
                }
            });

            return closestIdeology;
        },
        addScore: function(multiplier) {
            const effects = this.info.questions[this.questionIndex].effects;
            console.log(effects);
            Object.keys(effects).forEach((valueIndex) => {
                if (this.scores[Number(valueIndex)] == undefined) {
                    this.scores[Number(valueIndex)] = 0;
                }
                this.scores[Number(valueIndex)] += effects[valueIndex] * multiplier;
            });
            console.log(this.scores);
        },
        nextQuestion: function() {
            if (++this.questionIndex == this.info.questions.length) {
                this.questionIndex = 0;
                console.log("-- YOUR FINAL SCORES --");
                this.scorePercentages = this.calculatePercentageScores();
                this.currentPage = "results";
            }
        },
        reset: function() {
            this.scores = [];
            this.scorePercentages = [];
        },
    },
    mounted() {
        const urlParams = new URLSearchParams(window.location.search);
        this.infoSource = urlParams.get("id");
        fetch("https://gist.githubusercontent.com/raw/" + this.infoSource)
            .then((response) => {
                return response.text();
            })
            .then((text) => {
                console.log(text);
                this.info = JSON.parse(text);
            })
            .catch((error) => {
                console.error(error);
            });
    }
};
</script>

<style>

html {
    margin: 0 auto;
    max-width: 512px;
}

body {
    font-family: "JetBrains Mono", monospace;
    /*font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;*/
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #ebdbb2;
    background: #282828;
    margin: 128px auto;
    width: 100%;
    overflow-y: hidden;
}

.btn {
    display: block;
    align-self: center;
    padding: 16px;
    margin: 8px auto;
    border: none;
    border-radius: 8px;
    outline: none;
    font-size: 24px;
    color: #282828;
    user-select: none;
    transition-duration: 200ms;
}

.btn.accent {
    background-color: #8ec07c;
}

input {
    display: block;
    padding: 8px;
    margin: 8px;
    border: none;
    border-radius: 8px;
    outline: none;
    font-size: 16px;
    background: #282828;
    color: #ebdbb2;
}

.btn.small {
    display: inline;
}

.btn:hover {
    color: #ebdbb2;
    background-color: #504945;
    transition-duration: 100ms;
}

.btn:active {
    transform: translateY(4px);
}


h1, .header {
    color: #d79921;
    font-weight: 500;
    font-size: 40px;
}

.center {
    text-align: center;
}

.mute {
    color: #a89984;
}

.line {
    width: 100%;
    height: 2px;
    background-color: #3c3836;
    margin: 16px auto;
}
</style>
