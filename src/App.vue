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

let searchText = ref("");
const dialog = ref(false);

let loading = ref(false);

const locations = [...new Set(dataSet.map((entry) => entry.Location))];
locations.sort();

const uniqueMaterials = [...new Set(dataSet.map((entry) => entry.Material))];

let formattedMaterials = uniqueMaterials.map((material) => ({
  Material: material,
  sumOfQuantity: 0,
}));

const defaultValue = [...formattedMaterials];

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

function onInputQuantity(event, item) {
  loading.value = true;
  let sum = 0;

  const foundIndex = formattedMaterials.findIndex(
    (element) => element.Material === item.Material
  );

  if (foundIndex >= 0) {
    for (const key in item) {
      if (key.includes("Location")) {
        if (item[key]) sum += parseFloat(item[key]);
      }
    }
    formattedMaterials[foundIndex].sumOfQuantity = sum.toFixed(0);
  }

  loading.value = false;
}

function onClickSearchHandler() {
  loading.value = true;
  const filteredData = formattedMaterials.filter((item) =>
    item.Material.includes(searchText.value)
  );
  if (!searchText.value) formattedMaterials = [...defaultValue];
  else formattedMaterials = filteredData;

  loading.value = false;
}

const onAddMaterial = (event) => {
  defaultValue.splice(formattedMaterials.length, 0, {
    Material: event.Material,
    sumOfQuantity: event.sumOfQuantity,
  });

  formattedMaterials.splice(formattedMaterials.length, 0, {
    Material: event.Material,
    sumOfQuantity: event.sumOfQuantity,
  });

  onClickSearchHandler();
};

const onSubmitCycleCount = () => {
  const result = [];
  formattedMaterials.forEach((item) => {
    const material = item.Material;
    for (let i = 1; i <= locations.length; i++) {
      const location = `A${i}`;
      console.log(location);
      console.log();

      const quantity = parseFloat(item[`LocationA${i}`]) || 0;
      result.push({
        ID: result.length + 1,
        Material: material,
        Location: location,
        QTY: quantity.toFixed(0),
      });
    }
  });

  console.log(result);
};

export default {
  data: () => ({
    itemsPerPage: 20,
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
        <v-row align="end">
          <v-col cols="3">
            <v-text-field
              label="Material"
              v-model="searchText"
              desensity="compact"
              hide-details="auto"
            ></v-text-field>
          </v-col>

          <v-col cols="3" class="d-flex align-center">
            <v-btn prepend-icon="mdi-magnify" @click="onClickSearchHandler"
              >Search</v-btn
            >
          </v-col>

          <v-spacer></v-spacer>
          <v-col cols="3" class="d-flex justify-end">
            <v-btn
              prepend-icon="mdi-plus"
              color="success"
              @click="dialog = true"
              >New Material</v-btn
            >
          </v-col>

          <v-col cols="12">
            <v-data-table
              v-model:items-per-page="itemsPerPage"
              :headers="headers"
              :loading="loading"
              :items="formattedMaterials"
              item-value="Material"
              :itemsLength="dataSet.length"
            >
              <template v-slot:item="{ item }">
                <tr>
                  <td>
                    {{ item.Material }}
                  </td>

                  <td v-for="(location, indexCol) in locations" :key="location">
                    <v-text-field
                      label="Quantity"
                      v-model="item['Location' + location]"
                      @input="onInputQuantity($event, item)"
                      :tabindex="indexCol + 1"
                    ></v-text-field>
                  </td>

                  <td>
                    {{ item.sumOfQuantity }}
                  </td>
                </tr>
              </template>
            </v-data-table>
          </v-col>

          <v-col cols="12" class="d-flex justify-end mt-5">
            <v-btn @click="onSubmitCycleCount" color="primary"
              >Submit</v-btn
            ></v-col
          >
        </v-row>
      </v-container>
      <InsertMeterial
        v-model:modelValue="dialog"
        v-model:items="formattedMaterials"
        @onAddMaterial="onAddMaterial($event)"
      ></InsertMeterial>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from "vue";
import InsertMeterial from "@/components/cyclecount/InsertMaterial.vue";
</script>
