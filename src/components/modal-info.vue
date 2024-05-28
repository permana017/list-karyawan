<template>
  <v-dialog width="auto" v-model="dialog">
    <div v-if="isLoading" class="bg-white p-5 rounded-lg">
      <v-icon class="animate-spin" size="50" color="primary"
        >mdi-loading</v-icon
      >
      <p class="">Loading..</p>
    </div>
    <div v-else class="bg-white w-[100vw] rounded-lg max-w-xs">
      <div
        class="rounded-t-lg py-3 flex flex-col items-center justify-center bg-green-500"
      >
        <v-icon size="100" color="white">{{
          props.variant == `delete`
            ? `mdi-trash-can`
            : `mdi-check-circle-outline`
        }}</v-icon>
      </div>
      <div class="p-5 py-3">
        <p class="text-center text-lg font-semibold">Berhasil!</p>
        <p class="text-center">{{ message }}</p>
      </div>
      <div class="flex justify-center py-5">
        <v-btn color="primary" @click="emit('close')">Oke</v-btn>
      </div>
    </div>
  </v-dialog>
</template>
<script setup>
import { ref, watch } from "vue";

const props = defineProps({
  dialog: Boolean,
  isLoading: Boolean,
  message: "",
  variant: "",
});

const emit = defineEmits(["close", "refetch"]);

const dialog = ref(props.dialog);
watch(
  () => props.dialog,
  (newVal) => (dialog.value = newVal)
);
</script>
