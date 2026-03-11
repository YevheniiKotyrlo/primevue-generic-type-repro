<script setup lang="ts">
import { ref } from 'vue';
import Select from 'primevue/select';

interface User {
  id: number;
  name: string;
  email: string;
}

const users = ref<User[]>([
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' },
  { id: 3, name: 'Charlie', email: 'charlie@example.com' },
]);

const selectedUser = ref<User | null>(null);
</script>

<template>
  <div style="max-width: 600px; margin: 40px auto; font-family: system-ui, sans-serif">
    <h2>Select Generic Type Inference</h2>

    <p>
      The <code>#option</code> slot below accesses <code>option.naem</code> —
      a typo (<strong>naem</strong> instead of <strong>name</strong>).
    </p>

    <Select
      v-model="selectedUser"
      :options="users"
      option-label="name"
      placeholder="Select a user"
      style="width: 100%"
    >
      <template #option="{ option }">
        <div>{{ option.naem }} ({{ option.email }})</div>
      </template>
    </Select>

    <div style="margin-top: 24px; padding: 16px; background: #f8fafc; border-radius: 8px">
      <h3 style="margin: 0 0 8px">
        Run <code>bun run type-check</code> in the terminal
      </h3>
      <p style="margin: 0; color: #64748b">
        <strong>Without patch:</strong> 0 errors — the typo goes undetected
        because <code>option</code> is <code>any</code><br />
        <strong>With patch:</strong> TS2339 —
        <code>Property 'naem' does not exist on type 'User'</code>
      </p>
    </div>
  </div>
</template>
