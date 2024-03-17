<script>
const dataSet = [
  {
    ID: 1,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 2,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Location: "A2",
    QTY: 100,
  },
  {
    ID: 3,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Location: "A4",
    QTY: 100,
  },
  {
    ID: 4,
    Material: "MAT0002",
    ProductCode: "PC000002",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 5,
    Material: "MAT0002",
    ProductCode: "PC000002",
    Location: "A3",
    QTY: 100,
  },
  {
    ID: 6,
    Material: "MAT0003",
    ProductCode: "PC000003",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 7,
    Material: "MAT0004",
    ProductCode: "PC000004",
    Location: "A2",
    QTY: 100,
  },
];

let loading = ref(true);

const locations = [...new Set(dataSet.map((entry) => entry.Location))];
locations.sort();

const uniqueMaterials = [...new Set(dataSet.map((entry) => entry.Material))];

let formattedMaterials = uniqueMaterials.map((material) => ({
  Material: material,
  sumOfQuantity: 0,
}));

const headers = [
  {
    title: "Material",
    align: "start",
    sortable: false,
    key: "Material",
  },
];

const sumObject = {
  title: "Sum",
  align: "start",
  sortable: false,
  key: "sumOfQuantity",
};

locations.forEach((entry) => {
  headers.push({
    title: entry,
    align: "start",
    sortable: false,
    key: entry,
  });
});

headers.push(sumObject);

function onInputQuantity(item) {
  loading.value = true;
  let sum = 0;

  const foundIndex = formattedMaterials.findIndex(
    (element) => element.Material === item.Material
  );

  if (foundIndex >= 0) {
    for (const key in item) {
      if (key.includes("Location")) {
        sum += parseFloat(item[key]);
      }
    }

    formattedMaterials[foundIndex].sumOfQuantity = sum.toFixed(2);
  }

  loading.value = false;
}

export default {
  data: () => ({
    itemsPerPage: 20,
    newItems: [],
  }),
};
</script>

<template>
  <v-app>
    <v-app-bar :elevation="2">
      <v-app-bar-title>SCG Test</v-app-bar-title>
    </v-app-bar>

    <v-main class="mt-4">
      <v-container>
        <v-row no-gutters>
          <v-col cols="3">
            <v-text-field label="Material" dense="small"></v-text-field>
          </v-col>

          <v-col cols="2" class="d-flex justify-center align-center">
            <v-btn prepend-icon="mdi-magnify">Search</v-btn>
          </v-col>

          <v-col cols="12">
            <v-data-table
              v-model:items-per-page="itemsPerPage"
              :headers="headers"
              :loading="loading"
              :items="formattedMaterials"
              item-value="Material"
              :itemsLength="dataSet.length"
              @update:options="onInputQuantity"
            >
              <template #Material="{ props }">
                {{ props.Material }}
              </template>

              <template v-slot:[`item.A1`]="{ item }">
                <v-text-field
                  v-model="item['Location' + 'A1']"
                  label="Quantity"
                  @input="onInputQuantity(item)"
                ></v-text-field
              ></template>

              <template v-slot:[`item.A2`]="{ item }">
                <v-text-field
                  label="Quantity"
                  v-model="item['Location' + 'A2']"
                  @input="onInputQuantity(item)"
                ></v-text-field
              ></template>

              <template v-slot:[`item.A3`]="{ item }">
                <v-text-field
                  label="Quantity"
                  v-model="item['Location' + 'A3']"
                  @input="onInputQuantity(item)"
                ></v-text-field
              ></template>

              <template v-slot:[`item.A4`]="{ item }">
                <v-text-field
                  label="Quantity"
                  v-model="item['Location' + 'A4']"
                  @input="onInputQuantity(item)"
                ></v-text-field
              ></template>

              <template v-slot:[`item.sumOfQuantity`]="{ item }">
                {{ item.sumOfQuantity }}
              </template>
            </v-data-table>
          </v-col>
          <v-col cols="12"
            ><v-btn @click="console.log(formattedMaterials)">OK</v-btn></v-col
          >
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import CycleCountTable from "@/components/CycleCountTable.vue";
import { ref } from "vue";
</script>
