// Style and summary. I got form AI.
<template>
    <div class="min-h-screen bg-gray-100 flex items-center justify-center p-6">
        <div class="bg-white rounded-2xl shadow-lg p-8 w-full max-w-md">

            <!-- Title -->
            <h1 class="text-2xl font-bold text-center text-blue-600 mb-6">
                Student Grade Calculator
            </h1>

            <!-- Student Info -->
            <div class="space-y-3 mb-6">
                <div class="flex justify-between bg-gray-50 rounded-lg px-4 py-2">
                    <span class="text-gray-500 font-medium">Student Name</span>
                    <span class="text-gray-800 font-semibold">{{ studentName }}</span>
                </div>
                <div class="flex justify-between bg-gray-50 rounded-lg px-4 py-2">
                    <span class="text-gray-500 font-medium">Average Score</span>
                    <span class="text-blue-600 font-semibold">{{ averages.toFixed(2) }}</span>
                </div>
                <div class="flex justify-between bg-gray-50 rounded-lg px-4 py-2">
                    <span class="text-gray-500 font-medium">Highest Score</span>
                    <span class="text-green-600 font-semibold">{{ highScore.subject }} ({{ highScore.score }})</span>
                </div>
                <div class="flex justify-between bg-gray-50 rounded-lg px-4 py-2">
                    <span class="text-gray-500 font-medium">Lowest Score</span>
                    <span class="text-red-500 font-semibold">{{ lowScore.subject }} ({{ lowScore.score }})</span>
                </div>
                <div class="flex justify-between bg-gray-50 rounded-lg px-4 py-2">
                    <span class="text-gray-500 font-medium">Grade</span>
                    <span class="text-purple-600 font-bold text-lg">{{ grade }}</span>
                </div>
            </div>

            <!-- Divider -->
            <hr class="border-gray-200 mb-6">

            <!-- Summary -->
            <div class="bg-blue-50 border border-blue-200 rounded-lg p-4">
                <p class="text-sm font-semibold text-blue-600 mb-1">Summary</p>
                <p class="text-gray-700 text-sm leading-relaxed">{{ summary }}</p>
            </div>

        </div>
    </div>
</template>

<script setup>
    import { computed, ref } from 'vue';

    const studentName = ref('Narith HEN');
    const score = ref([
        {subject: 'Engliish', score: 60},
        {subject: 'PL', score: 70},
        {subject: 'Vue.js', score: 80}
    ]);
    const passMark = ref(50);

    //Averages
    const averages = computed(() => {
        let total = 0;
        score.value.forEach(function(subject) {
            total += subject.score;
        });
        return total / score.value.length;
    });

    //High Score
    const highScore = computed(() => {
        let high = score.value[0];
        score.value.forEach(function(subject) {
            if (subject.score > high.score) {
                high = subject;
            }
        });
        return high;
    });

    //Lowest Score
    const lowScore = computed(() => {
        let low = score.value[0];
        score.value.forEach(function(subject) {
            if (subject.score < low.score) {
                low = subject;
            }
        });
        return low;
    });

    // Grade
    const grade = computed(() => {
        if (averages.value >= 90) return 'A';
        if (averages.value >= 80) return 'B';
        if (averages.value >= 70) return 'C';
        if (averages.value >= 60) return 'D';
        return 'F';
    });

    //Summary
    const summary = computed(() => {
        const status = averages.value >= passMark.value ? 'PASSED' : 'FAILED';
        return studentName.value + ' scored an average of ' + averages.value.toFixed(2) + '. '
            + 'Best subject: ' + highScore.value.subject + ' (' + highScore.value.score + '). '
            + 'Weakest subject: ' + lowScore.value.subject + ' (' + lowScore.value.score + '). '
            + 'Grade: ' + grade.value + '. Status: ' + status + '.';
    });
</script>