<template>
  <mainLayout>
    <p class="font-semibold text-lg mb-3">List Karyawan</p>
    <v-data-table
      :headers="headers"
      :items="items"
      item-key="name"
      items-per-page="5"
      :search="search"
      :loading="isLoading"
    >
      <template v-slot:top>
        <div class="p-3 pt-5 flex justify-between">
          <v-text-field
            density="compact"
            model-value="John Doe"
            variant="outlined"
            placeholder="Search"
            hide-details
            class="max-w-xs"
            v-model="search"
          >
            <template v-slot:prepend-inner>
              <v-icon icon="mdi-magnify" class="text-black" /> </template
          ></v-text-field>
          <v-btn
            color="primary"
            text-transform="none"
            class="transform-none"
            @click="handelAddData"
            ><p>Tambah Data</p></v-btn
          >
        </div>
      </template>
      <template #item.salary="{ value }">
        <p class="">
          {{ formatCurrency(value) }}
        </p>
      </template>
      <template #item.is_merried="{ value }">
        <p class="">
          {{ value ? "Menikah" : "Belum Menikah" }}
        </p>
      </template>
      <template #item.action="{ item }">
        <v-icon
          color="green"
          class="mr-3 cursor-pointer"
          @click="handelUpdate(item)"
          >mdi-pencil</v-icon
        >
        <v-icon color="red" class="cursor-pointer" @click="handelDelete(item)"
          >mdi-delete
        </v-icon>
      </template>
    </v-data-table>
    <form-karyawan
      :field="field"
      @close="dialog = false"
      v-model="dialog"
      :action="actionType"
      @refetch="getApi"
    />
    <modal-info
      v-model="deleted.dialog"
      :isLoading="deleted.loading"
      variant="delete"
      message="Menghapus Data!"
      @close="deleted.dialog = false"
    />
  </mainLayout>
</template>
<script setup>
import axios from "axios";
import { onMounted, reactive, ref } from "vue";
import mainLayout from "../../components/main-layout.vue";
import axioInstance from "../../utils/axios";
import formKaryawan from "./form-karyawan.vue";
import ModalInfo from "../../components/modal-info.vue";

const items = ref([]);
const actionType = ref("post");
const isLoading = ref(false);
const deleted = reactive({
  loading: false,
  dialog: false,
});
const field = reactive({
  name: "",
  title: "",
  address: "",
  salary: "",
  is_merried: false,
  id: "",
});
const dialog = ref(false);
const search = ref("");
const headers = [
  { title: "Nama Karyawan", key: "name" },
  { title: "Jabatan", key: "title" },
  { title: "Alamat", key: "address" },
  { title: "Gaji", key: "salary" },
  { title: "Status Pernikahan", key: "is_merried" },
  { title: "Action", key: "action" },
];

const handelAddData = () => {
  actionType.value = "post";
  dialog.value = true;
  for (const key in field) {
    field[key] = "";
  }
  field = null;
};
const handelUpdate = (item) => {
  actionType.value = "update";
  dialog.value = true;
  for (const key in field) {
    for (const keyItem in item) {
      if (key == keyItem) {
        field[key] = item[keyItem];
      }
    }
  }
};
const handelDelete = async (item) => {
  deleted.loading = true;
  deleted.dialog = true;
  try {
    const res = await axioInstance.delete(`karyawan/${item.id}`);
    getApi();
  } catch (error) {
    console.log(error);
  } finally {
    deleted.loading = false;
  }
};

const getApi = async () => {
  try {
    const res = await axioInstance.get("karyawan");
    items.value = res.data;
  } catch (error) {
    console.log(error);
  } finally {
    isLoading.value = false;
  }
};

function formatCurrency(value) {
  if (value === undefined || value === null || value === "") {
    return 0;
  }

  // Mengkonversi value menjadi string
  let str = value.toString();

  // Menghapus bagian desimal jika ada
  let cleanStr = str.split(".")[0];

  // Menambahkan titik sebagai pemisah ribuan
  let formattedNum = cleanStr.replace(/\B(?=(\d{3})+(?!\d))/g, ".");

  return formattedNum;
}

onMounted(() => {
  isLoading.value = true;
  getApi();
});
</script>
