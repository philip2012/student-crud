<!-- src/assets/Components/StudentStatistics.vue -->
<template>
  <section
    class="w-full rounded-3xl bg-white shadow-xl ring-1 ring-black/5 p-8"
  >
    <h2 class="text-3xl font-semibold text-slate-900 mb-6">Statistics</h2>

    <div class="rounded-2xl border border-slate-200 bg-sky-50/70 p-6">
      <p class="text-slate-600 font-medium">Total Students</p>
      <p class="mt-2 text-5xl font-bold text-blue-600 leading-none">
        {{ stats.totalStudents }}
      </p>
    </div>

    <div class="mt-6 grid grid-cols-1 sm:grid-cols-3 gap-6">
      <div class="rounded-2xl border border-slate-200 bg-emerald-50/70 p-6">
        <p class="text-slate-600 font-medium">Avg Math</p>
        <p class="mt-3 text-4xl font-bold text-emerald-600 leading-none">
          {{ stats.avgMath.toFixed(1) }}
        </p>
      </div>

      <div class="rounded-2xl border border-slate-200 bg-sky-50/70 p-6">
        <p class="text-slate-600 font-medium">Avg Science</p>
        <p class="mt-3 text-4xl font-bold text-sky-600 leading-none">
          {{ stats.avgScience.toFixed(1) }}
        </p>
      </div>

      <div class="rounded-2xl border border-slate-200 bg-violet-50/70 p-6">
        <p class="text-slate-600 font-medium">Avg English</p>
        <p class="mt-3 text-4xl font-bold text-violet-600 leading-none">
          {{ stats.avgEnglish.toFixed(1) }}
        </p>
      </div>
    </div>

    <div class="mt-6 rounded-2xl border border-amber-200/70 bg-amber-50/70 p-6">
      <p class="text-slate-700 font-medium">Average Overall Score</p>

      <div class="mt-3 flex items-end gap-3">
        <p class="text-5xl font-extrabold text-amber-600 leading-none">
          {{ stats.avgOverall.toFixed(1) }}
        </p>
      </div>

      <div class="mt-5 h-3 w-full rounded-full bg-slate-200 overflow-hidden">
        <div
          class="h-full rounded-full bg-amber-500"
          :style="{ width: `${overallPercent}%` }"
        ></div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  students: {
    type: Array,
    required: true,
  },
});

const stats = computed(() => {
  const list = props.students;
  const total = list.length;

  if (!total) {
    return {
      totalStudents: 0,
      avgMath: 0,
      avgScience: 0,
      avgEnglish: 0,
      avgOverall: 0,
    };
  }

  const sum = list.reduce(
    (acc, s) => {
      acc.math += s.math;
      acc.science += s.science;
      acc.english += s.english;
      return acc;
    },
    { math: 0, science: 0, english: 0 }
  );

  return {
    totalStudents: total,
    avgMath: sum.math / total,
    avgScience: sum.science / total,
    avgEnglish: sum.english / total,
    avgOverall: (sum.math + sum.science + sum.english) / (3 * total),
  };
});

const overallPercent = computed(() =>
  Math.min(100, Math.max(0, stats.value.avgOverall))
);
</script>
