<template>
  <v-dialog width="auto" v-model="dialog">
    <div class="w-[100vw] max-w-xl bg-white min-h-[50vh] rounded-md">
      <div class="flex justify-between items-center p-3 border-b">
        <p class="font-semibold">
          {{ action == "post" ? "Tambah" : "Edit" }} Karyawan
        </p>
        <v-icon size="22" color="red" @click="$emit('close')">mdi-close</v-icon>
      </div>
      <v-form
        ref="form"
        class="p-4"
        validate-on="submit lazy"
        @submit.prevent="onSubmit"
      >
        <v-text-field
          label="Nama Karyawan"
          density="compact"
          :rules="rulesName"
          placeholder="Masukan Nama Karyawan"
          v-model="field.name"
        ></v-text-field>
        <v-text-field
          label="Jabatan"
          :rules="rulesTitle"
          density="compact"
          placeholder="Masukan Nama Karyawan"
          v-model="field.title"
        ></v-text-field>
        <v-text-field
          label="Alamat"
          density="compact"
          placeholder="Masukan Nama Karyawan"
          v-model="field.address"
        ></v-text-field>
        <v-text-field
          :rules="rulesSalary"
          label="Gaji"
          density="compact"
          placeholder="Masukan Nominal Gaji"
          type="number"
          v-model="field.salary"
        >
          <template v-slot:prepend-inner>
            <div class="text-gray-500 font-semibold pr-1">Rp</div>
          </template>
        </v-text-field>
        <v-select
          density="compact"
          label="Status Pernikahan"
          :items="optionMarried"
          item-value="value"
          item-title="label"
          placeholder="Masukan Nama Karyawan"
          v-model="field.is_merried"
        ></v-select>
        <div class="flex gap-3 justify-end">
          <v-btn color="grey lighten-1" @click="$emit('close')">Cancle</v-btn>
          <v-btn type="submit" color="success">Save</v-btn>
        </div>
      </v-form>
    </div>
    <modal-info
      @close="closeDialog"
      v-model="dialogPost"
      :isLoading="isLoading"
      :message="message"
    />
  </v-dialog>
</template>
<script setup>
import { ref, watch } from "vue";
import axiosInstance from "../../utils/axios";
import modalInfo from "../../components/modal-info.vue";

const props = defineProps({
  dialog: Boolean,
  field: null,
  action: "post",
});

const emit = defineEmits(["close", "refetch"]);

const dialog = ref(props.dialog);
const dialogPost = ref(false);
const form = ref(null);
const isLoading = ref(false);
const message = ref("");

watch(
  () => props.dialog,
  (newVal) => (dialog.value = newVal)
);

const rulesName = [(value) => !!value || `Nama Karyawan harus di isi `];
const rulesTitle = [(value) => !!value || `Jabatan harus di isi `];
const rulesSalary = [(value) => !!value || `Gaji harus di isi `];

const optionMarried = [
  {
    label: "Belum Menikah",
    value: false,
  },
  {
    label: "Sudah Menikah",
    value: true,
  },
];

const postData = async () => {
  dialogPost.value = true;
  isLoading.value = true;
  const isPost = props.action == "post";
  try {
    const res = isPost
      ? await axiosInstance.post("karyawan", props.field)
      : await axiosInstance.put(`karyawan/${props.field?.id}`, props.field);
    emit("refetch", props.action);
    message.value = `berhasil ${
      props.action == "post" ? "menambahkan" : "mengubah"
    } data`;
  } catch (error) {
    console.log(error);
  } finally {
    isLoading.value = false;
  }
};

const closeDialog = () => {
  dialogPost.value = false;
  emit("close");
};

const onSubmit = async (event) => {
  const { valid } = await form.value.validate();
  if (valid) {
    postData();
  }
};
</script>
