<!-- src/app.vue -->
<script setup>
import { ref, computed, onMounted, watch } from "vue";
import AddStudent from "./assets/Components/AddStudent.vue";
import RecordTable from "./assets/Components/RecordTable.vue";
import StudentStatistics from "./assets/Components/StudentStatistics.vue";

const students = ref([]);
const editingStudent = ref(null);
const nameQuery = ref("");

// --- sort state (default: new -> old) --- //
const sortKey = ref("id"); // id = new/old
const sortDir = ref("desc"); // desc = new -> old

const avgOf = (s) => (s.math + s.science + s.english) / 3;

const setSort = (key) => {
  if (sortKey.value === key) {
    sortDir.value = sortDir.value === "asc" ? "desc" : "asc";
    return;
  }

  sortKey.value = key;
  sortDir.value = "asc";
};

const displayStudents = computed(() => {
  const q = nameQuery.value.trim().toLowerCase();

  const filtered = !q
    ? students.value
    : students.value.filter((s) =>
        `${s.firstName} ${s.lastName}`.toLowerCase().includes(q)
      );

  const dir = sortDir.value === "asc" ? 1 : -1;

  const avgOf = (s) => (s.math + s.science + s.english) / 3;

  const getValue = (s) => {
    if (sortKey.value === "id") return s.id;
    if (sortKey.value === "student")
      return `${s.firstName} ${s.lastName}`.trim().toLowerCase();
    if (sortKey.value === "math") return s.math;
    if (sortKey.value === "science") return s.science;
    if (sortKey.value === "english") return s.english;
    if (sortKey.value === "average") return avgOf(s);
    if (sortKey.value === "ranking") return avgOf(s);
    return s.id;
  };

  return [...filtered].sort((a, b) => {
    const av = getValue(a);
    const bv = getValue(b);

    if (typeof av === "string" && typeof bv === "string") {
      return av.localeCompare(bv) * dir;
    }

    if (av < bv) return -1 * dir;
    if (av > bv) return 1 * dir;
    return b.id - a.id;
  });
});

const initData = () => {
  const sample = [
    {
      id: 1,
      firstName: "Alice",
      lastName: "Johnson",
      math: 92,
      science: 85,
      english: 88,
    },
    {
      id: 2,
      firstName: "Bob",
      lastName: "Smith",
      math: 76,
      science: 81,
      english: 79,
    },
    {
      id: 3,
      firstName: "Charlie",
      lastName: "Brown",
      math: 65,
      science: 70,
      english: 72,
    },
  ];
  students.value = sample;
};

watch(
  students,
  (newVal) => {
    localStorage.setItem("students", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(() => {
  const saved = localStorage.getItem("students");
  if (saved) {
    try {
      students.value = JSON.parse(saved);
    } catch (e) {
      console.error("Failed to parse saved students:", e);
      initData();
    }
  } else {
    initData();
  }
});

const nextId = () =>
  students.value.length ? Math.max(...students.value.map((s) => s.id)) + 1 : 1;

const addStudent = (data) => {
  const { id, ...rest } = data; // bỏ id từ form (thường null)
  students.value = [...students.value, { ...rest, id: nextId() }];
};

const startEdit = (student) => {
  editingStudent.value = { ...student };
};

const updateStudent = (updated) => {
  students.value = students.value.map((s) =>
    s.id === updated.id ? { ...updated } : s
  );
  editingStudent.value = null;
};

const deleteStudent = (id) => {
  students.value = students.value.filter((s) => s.id !== id);
  if (editingStudent.value?.id === id) editingStudent.value = null;
};

// optional (nếu muốn dùng ở đâu đó)
const studentCount = computed(() => students.value.length);
const globalAverage = computed(() => {
  if (!students.value.length) return "0.0";
  const total = students.value.reduce(
    (sum, s) => sum + (s.math + s.science + s.english) / 3,
    0
  );
  return (total / students.value.length).toFixed(1);
});
</script>

<template>
  <main class="min-h-screen bg-gray-50 text-sm">
    <div class="mx-auto max-w-8xl px-6 pt-10 flex flex-col gap-6">
      <h1 class="text-2xl font-semibold text-center">
        Student Management System (CRUD)
      </h1>

      <p class="text-gray-500 text-center">
        Manage each student with CRUD operations with ease
      </p>

      <div class="w-full grid grid-cols-1 md:grid-cols-2 gap-6 items-start">
        <AddStudent
          :student="editingStudent"
          @add-student="addStudent"
          @update-student="updateStudent"
          @cancel-edit="editingStudent = null"
        />

        <StudentStatistics :students="students" />
      </div>

      <RecordTable
        :students="displayStudents"
        :sort-key="sortKey"
        :sort-dir="sortDir"
        :name-query="nameQuery"
        @sort="setSort"
        @edit="startEdit"
        @delete="deleteStudent"
        @update:name-query="nameQuery = $event"
      />
    </div>
  </main>
</template>
