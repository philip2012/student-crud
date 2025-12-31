<!-- src/assets/Components/RecordTable.vue -->
<template>
  <section
    class="w-full rounded-3xl bg-white shadow-xl ring-1 ring-black/5 overflow-hidden"
  >
    <div class="px-8 py-6 flex items-start justify-between">
      <div>
        <h2 class="text-3xl font-semibold text-slate-900">Student Records</h2>
        <p class="mt-1 text-slate-500">
          {{ students.length }} student<span v-if="students.length !== 1"
            >s</span
          >
          found
        </p>
      </div>

      <p class="text-slate-500">Showing all students</p>
    </div>

    <div class="h-px bg-slate-200"></div>

    <div class="w-full overflow-x-auto">
      <table class="w-full min-w-275">
        <thead class="bg-slate-50">
          <tr
            class="text-left text-xs font-medium tracking-widest text-slate-500 uppercase"
          >
            <th class="px-8 py-4 w-55">ID</th>
            <th class="px-8 py-4 w-[320px]">Student Info</th>
            <th class="px-8 py-4 w-40">Math</th>
            <th class="px-8 py-4 w-40">Science</th>
            <th class="px-8 py-4 w-40">English</th>
            <th class="px-8 py-4 w-55">Average</th>
            <th class="px-8 py-4 w-40">Ranking</th>
            <th class="px-8 py-4 w-55">Actions</th>
          </tr>
        </thead>

        <tbody class="divide-y divide-slate-200">
          <tr v-if="students.length === 0">
            <td colspan="8" class="px-8 py-10 text-slate-500">
              No students yet. Add one on the left.
            </td>
          </tr>

          <tr v-for="s in students" :key="s.id" class="align-middle">
            <td class="px-8 py-6">
              <div class="flex items-center gap-4">
                <div
                  class="h-16 w-16 rounded-full bg-blue-50 flex items-center justify-center font-semibold text-blue-700"
                >
                  #{{ String(s.id).padStart(3, "0") }}
                </div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-2xl font-semibold text-slate-900 leading-tight">
                {{ s.firstName }} {{ s.lastName }}
              </p>
              <p class="mt-2 text-slate-500">
                Student ID: STU{{ String(s.id).padStart(4, "0") }}
              </p>
            </td>

            <td class="px-8 py-6">
              <p class="text-2xl font-semibold text-slate-900">{{ s.math }}</p>
              <div
                class="mt-3 h-3 w-28 rounded-full bg-slate-200 overflow-hidden"
              >
                <div
                  class="h-full rounded-full bg-orange-500"
                  :style="{ width: `${s.math}%` }"
                ></div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-2xl font-semibold text-slate-900">
                {{ s.science }}
              </p>
              <div
                class="mt-3 h-3 w-28 rounded-full bg-slate-200 overflow-hidden"
              >
                <div
                  class="h-full rounded-full bg-orange-500"
                  :style="{ width: `${s.science}%` }"
                ></div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-2xl font-semibold text-slate-900">
                {{ s.english }}
              </p>
              <div
                class="mt-3 h-3 w-28 rounded-full bg-slate-200 overflow-hidden"
              >
                <div
                  class="h-full rounded-full bg-emerald-500"
                  :style="{ width: `${s.english}%` }"
                ></div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-4xl font-semibold text-slate-900">{{ avg(s) }}</p>
              <div
                class="mt-3 h-3 w-36 rounded-full bg-slate-200 overflow-hidden"
              >
                <div
                  class="h-full rounded-full bg-amber-500"
                  :style="{ width: `${avg(s)}%` }"
                ></div>
              </div>
            </td>

            <td class="px-8 py-6">
              <span
                class="inline-flex items-center justify-center px-10 py-3 rounded-full text-white font-semibold"
                :class="rankClass(s)"
              >
                {{ rank(s) }}
              </span>
            </td>

            <td class="px-8 py-6">
              <div class="flex items-center gap-4">
                <button
                  class="px-8 py-4 rounded-2xl bg-blue-600 text-white text-lg font-semibold shadow-lg shadow-blue-200 hover:bg-blue-700"
                  @click="emit('edit', s)"
                >
                  Edit
                </button>

                <button
                  class="px-8 py-4 rounded-2xl bg-red-600 text-white text-lg font-semibold shadow-lg shadow-red-200 hover:bg-red-700"
                  @click="emit('delete', s.id)"
                >
                  Delete
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="h-px bg-slate-200"></div>

    <div class="px-8 py-5 flex items-center justify-between text-slate-600">
      <p>
        <span class="font-semibold text-slate-700">Summary:</span>
        Highest Average: {{ summary.highest }}, Lowest Average:
        {{ summary.lowest }}
      </p>

      <p class="flex items-center gap-3">
        <span class="font-semibold text-slate-700"
          >Overall Class Performance:</span
        >
        <span
          class="px-4 py-2 rounded-full bg-blue-50 text-blue-700 font-semibold"
        >
          {{ summary.performance }}
        </span>
      </p>
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

const emit = defineEmits(["edit", "delete"]);

const avg = (s) => ((s.math + s.science + s.english) / 3).toFixed(1);

const rank = (s) => {
  const a = Number(avg(s));
  if (a >= 90) return "A";
  if (a >= 80) return "B";
  if (a >= 70) return "C";
  if (a >= 60) return "D";
  return "F";
};

const rankClass = (s) => {
  const r = rank(s);
  if (r === "A") return "bg-emerald-600";
  if (r === "B") return "bg-sky-600";
  if (r === "C") return "bg-amber-600";
  if (r === "D") return "bg-orange-600";
  return "bg-red-600";
};

const summary = computed(() => {
  const list = props.students;
  if (!list.length) {
    return { highest: "0.0", lowest: "0.0", performance: "N/A" };
  }

  const avgs = list.map((s) => Number(avg(s)));
  const highest = Math.max(...avgs).toFixed(1);
  const lowest = Math.min(...avgs).toFixed(1);

  const overall = avgs.reduce((sum, x) => sum + x, 0) / avgs.length;
  const performance =
    overall >= 85
      ? "Excellent"
      : overall >= 70
      ? "Good"
      : overall >= 50
      ? "Average"
      : "Poor";

  return { highest, lowest, performance };
});
</script>
