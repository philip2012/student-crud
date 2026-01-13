<!-- src/assets/Components/RecordTable.vue -->
<template>
  <section
    class="w-full rounded-3xl bg-white shadow-xl ring-1 ring-black/5 overflow-hidden text-sm"
  >
    <div class="px-8 py-6 flex items-start justify-between">
      <div>
        <h2 class="text-2xl font-semibold text-slate-900">Student Records</h2>
        <p class="mt-1 text-slate-500">
          {{ props.students.length }} student<span
            v-if="props.students.length !== 1"
            >s</span
          >
          found
        </p>
      </div>

      <div class="flex flex-row gap-4 items-center">
        <label
          class="inline-flex items-center gap-3 cursor-pointer select-none"
        >
          <input
            type="checkbox"
            class="peer sr-only"
            @change="
              listMode = $event.target.checked ? 'infinite' : 'pagination'
            "
            :checked="listMode === 'infinite'"
          />

          <div
            class="relative h-6 w-11 rounded-full bg-blue-600 ring-1 ring-black/10 transition peer-focus-visible:ring-2 peer-focus-visible:ring-blue-500 after:content-[''] after:absolute after:top-0.5 after:left-0.5 after:h-5 after:w-5 after:rounded-full after:bg-white after:shadow after:transition-transform peer-checked:after:translate-x-5"
          ></div>

          <span class="text-sm font-medium text-gray-900">{{
            capitalizedModeString
          }}</span>
        </label>

        <input
          type="text"
          name="search"
          id="search"
          placeholder="Enter name here"
          class="w-105 rounded-lg border border-slate-200 bg-white px-4 py-2 text-slate-900 placeholder:text-slate-400 outline-none focus:ring-2 focus:ring-blue-500/30"
          :value="nameQuery"
          @input="emit('update:name-query', $event.target.value)"
        />

        <p class="text-slate-500">
          {{
            nameQuery ? `Filtering by "${nameQuery}"` : "Showing all students"
          }}
        </p>
      </div>
    </div>

    <div class="h-px bg-slate-200"></div>

    <div class="w-full overflow-x-auto">
      <table class="w-full min-w-275">
        <thead class="bg-slate-50">
          <tr
            class="text-left text-xs font-medium tracking-widest text-slate-500 uppercase"
          >
            <th class="px-8 py-4 w-55">
              <button class="hover:text-slate-900" @click="emit('sort', 'id')">
                ID{{ arrow("id") }}
              </button>
            </th>

            <th class="px-8 py-4 w-[320px]">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'student')"
              >
                Student Info{{ arrow("student") }}
              </button>
            </th>

            <th class="px-8 py-4 w-40">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'math')"
              >
                Math{{ arrow("math") }}
              </button>
            </th>

            <th class="px-8 py-4 w-40">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'science')"
              >
                Science{{ arrow("science") }}
              </button>
            </th>

            <th class="px-8 py-4 w-40">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'english')"
              >
                English{{ arrow("english") }}
              </button>
            </th>

            <th class="px-8 py-4 w-55">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'average')"
              >
                Average{{ arrow("average") }}
              </button>
            </th>

            <th class="px-8 py-4 w-40">
              <button
                class="hover:text-slate-900"
                @click="emit('sort', 'ranking')"
              >
                Ranking{{ arrow("ranking") }}
              </button>
            </th>

            <th class="px-8 py-4 w-55">Actions</th>
          </tr>
        </thead>

        <tbody class="divide-y divide-slate-200">
          <tr v-if="props.students.length === 0">
            <td colspan="8" class="px-8 py-10 text-slate-500">
              No students yet. Add one on the left.
            </td>
          </tr>

          <tr v-for="s in studentsToRender" :key="s.id" class="align-middle">
            <td class="px-8 py-6">
              <div class="flex items-center gap-4">
                <div
                  class="h-16 w-14 rounded-full bg-blue-50 flex items-center justify-center font-semibold text-blue-700"
                >
                  #{{ String(s.id).padStart(3, "0") }}
                </div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-lg font-semibold text-slate-900 leading-tight">
                {{ s.firstName }} {{ s.lastName }}
              </p>
              <p class="mt-2 text-slate-500">
                Student ID: STU{{ String(s.id).padStart(4, "0") }}
              </p>
            </td>

            <td class="px-8 py-6">
              <p class="text-lg font-semibold text-slate-900">{{ s.math }}</p>
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
              <p class="text-lg font-semibold text-slate-900">
                {{ s.science }}
              </p>
              <div
                class="mt-3 h-3 w-28 rounded-full bg-slate-200 overflow-hidden"
              >
                <div
                  class="h-full rounded-full bg-fuchsia-500"
                  :style="{ width: `${s.science}%` }"
                ></div>
              </div>
            </td>

            <td class="px-8 py-6">
              <p class="text-lg font-semibold text-slate-900">
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
              <p class="text-2xl font-semibold text-slate-900">{{ avg(s) }}</p>
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
                  class="px-8 py-4 rounded-2xl bg-blue-600 text-white text-sm font-semibold shadow-lg shadow-blue-200 hover:bg-blue-700"
                  @click="emit('edit', s)"
                >
                  Edit
                </button>

                <button
                  class="px-8 py-4 rounded-2xl bg-red-600 text-white text-sm font-semibold shadow-lg shadow-red-200 hover:bg-red-700"
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

    <div>
      <div
        v-if="listMode === 'pagination' && totalStudents > 0"
        class="px-8 py-6"
      >
        <nav aria-label="Pagination" class="flex justify-center">
          <ul class="flex items-center gap-2 text-sm">
            <!-- Prev -->
            <li>
              <button
                type="button"
                aria-label="Previous page"
                :disabled="page === 1"
                @click="page = Math.max(1, page - 1)"
                class="px-4 py-2 rounded-lg border border-slate-300 text-slate-700 hover:bg-slate-100 disabled:opacity-40 disabled:cursor-not-allowed"
              >
                Prev
              </button>
            </li>

            <!-- Page numbers -->
            <li v-for="p in totalPages" :key="p">
              <button
                type="button"
                :aria-current="p === page ? 'page' : null"
                @click="page = p"
                class="h-10 w-10 rounded-lg border flex items-center justify-center transition"
                :class="
                  p === page
                    ? 'bg-blue-600 border-blue-600 text-white font-semibold'
                    : 'border-slate-300 text-slate-700 hover:bg-slate-100'
                "
              >
                {{ p }}
              </button>
            </li>

            <!-- Next -->
            <li>
              <button
                type="button"
                aria-label="Next page"
                :disabled="page === totalPages"
                @click="page = Math.min(totalPages, page + 1)"
                class="px-4 py-2 rounded-lg border border-slate-300 text-slate-700 hover:bg-slate-100 disabled:opacity-40 disabled:cursor-not-allowed"
              >
                Next
              </button>
            </li>
          </ul>
        </nav>
      </div>

      <div
        v-if="listMode === 'infinite' && visibleCount < totalStudents"
        class="px-8 py-6 flex justify-center"
      >
        <button
          type="button"
          @click="visibleCount = Math.min(totalStudents, visibleCount + step)"
          class="inline-flex items-center gap-2 px-6 py-3 rounded-xl bg-blue-600 text-white font-semibold shadow-lg shadow-blue-200 hover:bg-blue-700 active:scale-[0.98] transition"
        >
          Load More
        </button>
      </div>
    </div>

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
import { computed, ref, watch } from "vue";

const props = defineProps({
  students: { type: Array, required: true },
  sortKey: { type: String, required: true },
  sortDir: { type: String, required: true },
  nameQuery: { type: String, required: true },
});

const listMode = ref("pagination");

const capitalizedModeString = computed(() => {
  return listMode.value.charAt(0).toUpperCase() + listMode.value.slice(1);
});

const page = ref(1);
const visibleCount = ref(15);

const pageSize = 5;
const step = 15;

const studentsToRender = computed(() => {
  const start = (page.value - 1) * pageSize;
  const end = start + pageSize;

  if (listMode.value === "pagination") {
    return props.students.slice(start, end);
  } else if (listMode.value === "infinite") {
    return props.students.slice(0, visibleCount.value);
  } else {
    return [];
  }
});

const totalStudents = computed(() => {
  return props.students.length;
});
const totalPages = computed(() => {
  if (totalStudents.value === 0) {
    return 1;
  }
  return Math.ceil(totalStudents.value / pageSize);
});

watch(totalPages, (newVal) => {
  page.value = Math.min(Math.max(1, page.value), newVal);
});

const emit = defineEmits(["edit", "delete", "sort", "update:name-query"]);

const arrow = (key) => {
  if (props.sortKey !== key) return "";
  return props.sortDir === "asc" ? " ▲" : " ▼";
};

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
