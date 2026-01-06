<!-- src/assets/Components/AddStudent.vue -->
<template>
  <section
    class="w-full rounded-3xl bg-white shadow-xl ring-1 ring-black/5 p-8 text-sm"
  >
    <div class="flex items-start justify-between gap-4">
      <h2 class="text-2xl font-semibold text-slate-900">
        {{ isEditing ? "Edit Student" : "Add New Student" }}
      </h2>

      <button
        v-if="isEditing"
        type="button"
        class="px-5 py-3 rounded-2xl bg-slate-100 text-slate-700 font-semibold hover:bg-slate-200"
        @click="cancel"
      >
        Cancel
      </button>
    </div>

    <div class="mt-6 grid grid-cols-1 md:grid-cols-2 gap-6">
      <div class="flex flex-col gap-2">
        <label class="text-slate-700 font-medium" for="firstName"
          >First Name *</label
        >
        <input
          id="firstName"
          v-model.trim="form.firstName"
          type="text"
          placeholder="Enter first name"
          class="h-12 w-full rounded-xl border border-slate-200 bg-white px-4 text-slate-900 placeholder:text-slate-400 outline-none focus:ring-2 focus:ring-blue-500/30"
        />
      </div>

      <div class="flex flex-col gap-2">
        <label class="text-slate-700 font-medium" for="lastName"
          >Last Name *</label
        >
        <input
          id="lastName"
          v-model.trim="form.lastName"
          type="text"
          placeholder="Enter last name"
          class="h-12 w-full rounded-xl border border-slate-200 bg-white px-4 text-slate-900 placeholder:text-slate-400 outline-none focus:ring-2 focus:ring-blue-500/30"
        />
      </div>
    </div>

    <h3 class="mt-8 text-lg font-semibold text-slate-900">Scores (0-100)</h3>

    <div class="mt-4 grid grid-cols-1 sm:grid-cols-3 gap-6">
      <div class="flex flex-col gap-2">
        <label class="text-slate-700 font-medium" for="mathScore">Math</label>
        <input
          id="mathScore"
          v-model.number="form.math"
          type="number"
          class="h-12 w-full rounded-xl border border-slate-200 bg-white px-4 text-slate-900 outline-none focus:ring-2 focus:ring-blue-500/30"
          @blur="clamp('math')"
        />
      </div>

      <div class="flex flex-col gap-2">
        <label class="text-slate-700 font-medium" for="scienceScore"
          >Science</label
        >
        <input
          id="scienceScore"
          v-model.number="form.science"
          type="number"
          class="h-12 w-full rounded-xl border border-slate-200 bg-white px-4 text-slate-900 outline-none focus:ring-2 focus:ring-blue-500/30"
          @blur="clamp('science')"
        />
      </div>

      <div class="flex flex-col gap-2">
        <label class="text-slate-700 font-medium" for="englishScore"
          >English</label
        >
        <input
          id="englishScore"
          v-model.number="form.english"
          type="number"
          class="h-12 w-full rounded-xl border border-slate-200 bg-white px-4 text-slate-900 outline-none focus:ring-2 focus:ring-blue-500/30"
          @blur="clamp('english')"
        />
      </div>
    </div>

    <div class="mt-8 rounded-2xl bg-slate-50 p-6">
      <div class="flex items-center justify-between">
        <h4 class="text-base font-semibold text-slate-900">
          Performance Summary
        </h4>
        <p class="text-slate-500">Average: {{ average }}</p>
      </div>

      <div class="mt-4 flex items-center justify-between">
        <p class="text-slate-600">Total Score:</p>
        <p class="text-base font-semibold text-slate-900">{{ total }}/300</p>
      </div>

      <div class="mt-4 h-3 w-full rounded-full bg-slate-200 overflow-hidden">
        <div
          class="h-full rounded-full bg-amber-500"
          :style="{ width: `${percent}%` }"
        ></div>
      </div>

      <div class="mt-4 flex items-center justify-between text-slate-400">
        <p>0</p>
        <p>50</p>
        <p>100</p>
      </div>
    </div>

    <button
      type="button"
      class="mt-8 h-14 w-full rounded-xl bg-blue-600 text-white text-base font-semibold shadow-sm hover:bg-blue-700 active:bg-blue-800 disabled:opacity-50"
      :disabled="!canSubmit"
      @click="submit"
    >
      {{ isEditing ? "Update Student" : "Add Student" }}
    </button>
  </section>
</template>

<script setup>
// --- imports --- //
import { ref, watch, computed } from "vue";

// --- props / emits --- //
const props = defineProps({
  student: {
    type: Object,
    default: null,
  },
});

const emit = defineEmits(["add-student", "update-student", "cancel-edit"]);

// --- form state --- //
const empty = () => ({
  id: null,
  firstName: "",
  lastName: "",
  math: 0,
  science: 0,
  english: 0,
});

const form = ref(empty());

const isEditing = computed(() => !!props.student);

// --- sync edit --- //
watch(
  () => props.student,
  (s) => {
    form.value = s ? { ...s } : empty();
  },
  { immediate: true }
);

// --- helpers --- //
const clamp = (k) => {
  form.value[k] = Math.min(100, Math.max(0, Number(form.value[k]) || 0));
};

const total = computed(() => {
  return (
    (Number(form.value.math) || 0) +
    (Number(form.value.science) || 0) +
    (Number(form.value.english) || 0)
  );
});

const average = computed(() => (total.value / 3).toFixed(1));
const percent = computed(() =>
  Math.min(100, Math.max(0, Number(average.value) || 0))
);

const canSubmit = computed(() => {
  return !!form.value.firstName.trim() && !!form.value.lastName.trim();
});

const cancel = () => {
  emit("cancel-edit");
};

const submit = () => {
  if (!canSubmit.value) return;

  clamp("math");
  clamp("science");
  clamp("english");

  const payload = {
    id: form.value.id,
    firstName: form.value.firstName.trim(),
    lastName: form.value.lastName.trim(),
    math: Number(form.value.math) || 0,
    science: Number(form.value.science) || 0,
    english: Number(form.value.english) || 0,
  };

  if (isEditing.value) {
    emit("update-student", payload);
  } else {
    emit("add-student", payload);
    form.value = empty();
  }
};
</script>
