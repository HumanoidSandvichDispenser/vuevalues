<template>
    <div class="progress-bar-component">
        <div class="label" :style="`color: ${leftColor};`">
            {{ leftLabel }} <span class="percentage">{{ progress }}%</span>
        </div>
        <div class="label right" :style="`color: ${rightColor};`">
            <span class="percentage">{{ 100 - progress }}%</span> {{ rightLabel }}
        </div>
        <div class="progress-bar-container" :style="`background-color: ${rightColor};`">
            <div class="progress-bar" :style="`background-color: ${leftColor}; width: ${progress}%`"></div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        leftColor: String,
        rightColor: String,
        leftLabel: String,
        rightLabel: String,
        centerLabel: String,
        progress: Number,
    },
}
</script>

<style>
.progress-bar-component {
    margin-bottom: 16px;
}

.label {
    display: inline;
    margin-left: 8px;
    margin-bottom: 8px;
}

.label.right {
    float: right;
    margin-right: 8px;
}

.progress-bar-container {
    margin: 8px;
}

.progress-bar-container, .progress-bar-container > div {
    height: 32px;
}

.progress-bar {
    border-right: #ffffff;
    border-right-width: 2px;
    border-right-style: dashed;
}

.progress-bar[width=100%], .progress-bar[width=0%] {
    border-right: none;
}

.percentage {
    color: #ebdbb2;
    margin: auto 8px;
}
</style>