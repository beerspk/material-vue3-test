<template>
  <template>
    <div class="text-center pa-4">
      <v-dialog v-model="model" max-width="600" persistent>
        <v-card title="Register Material">
          <template v-slot:text>
            <v-form @submit.prevent>
              <v-row class="pt-4" justify="center">
                <v-col cols="11">
                  <v-autocomplete
                    v-model="selectedMaterial"
                    :items="uniqueMaterialsWithInfo"
                    density="compact"
                    label="Search Material"
                    @change="onClearSearch"
                    item-title="Material"
                    item-value="Material"
                    return-object
                  ></v-autocomplete>
                </v-col>

                <v-col cols="3" class="d-flex"> Product Code : </v-col>

                <v-col cols="8">
                  {{
                    selectedMaterial ? selectedMaterial.ProductCode : ""
                  }}</v-col
                >

                <v-col cols="3" class="d-flex"> Description : </v-col>

                <v-col cols="8">
                  {{
                    selectedMaterial ? selectedMaterial.Description : ""
                  }}</v-col
                >

                <v-col
                  cols="11"
                  v-if="isMaterialFound(items, selectedMaterial.Material)"
                >
                  <div class="text-red">
                    This material has been successfully registered.
                  </div>
                </v-col>
              </v-row>
            </v-form>
          </template>

          <template v-slot:actions>
            <v-spacer></v-spacer>
            <v-btn
              @click="onAddMaterial(selectedMaterial)"
              color="primary"
              :disabled="isMaterialFound(items, selectedMaterial.Material)"
            >
              Add
            </v-btn>
            <v-btn @click="model = false"> Cancel </v-btn>
          </template>
        </v-card>
      </v-dialog>
    </div>
  </template>
</template>

<script setup>
import { ref } from "vue";
const model = defineModel({ default: false });
const items = defineModel("items");

const emit = defineEmits(["onAddMaterial"]);

const searchText = ref("");
const defaultObject = ref({ Material: "", Description: "", ProductCode: "" });
const selectedMaterial = ref(defaultObject.value);

const dataSet = [
  {
    ID: 1,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Description: "This is Material 1",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 2,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Description: "This is Material 1",
    Location: "A2",
    QTY: 100,
  },
  {
    ID: 3,
    Material: "MAT0001",
    ProductCode: "PC000001",
    Description: "This is Material 1",
    Location: "A4",
    QTY: 100,
  },
  {
    ID: 4,
    Material: "MAT0002",
    ProductCode: "PC000002",
    Description: "This is Material 1",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 5,
    Material: "MAT0002",
    ProductCode: "PC000002",
    Description: "This is Material 2",
    Location: "A3",
    QTY: 100,
  },
  {
    ID: 6,
    Material: "MAT0003",
    ProductCode: "PC000003",
    Description: "This is Material 3",
    Location: "A1",
    QTY: 100,
  },
  {
    ID: 7,
    Material: "MAT0004",
    ProductCode: "PC000004",
    Description: "This is Material 4",
    Location: "A2",
    QTY: 100,
  },
  {
    ID: 8,
    Material: "MAT0005",
    ProductCode: "PC000005",
    Description: "This is Material 5",
    Location: "A1",
    QTY: 100,
  },
];

const onClearSearch = () => {
  console.log("onClearSearch");
  selectedMaterial.value = defaultObject.value;
};

const onSelectedSearch = () => {
  console.log("onSelectedSearch");
};

const uniqueMaterials = {};

dataSet.forEach((item) => {
  if (!uniqueMaterials.hasOwnProperty(item.Material)) {
    uniqueMaterials[item.Material] = {
      Description: item.Description,
      ProductCode: item.ProductCode,
      Locations: [],
    };
  }
  uniqueMaterials[item.Material].Locations.push(item.Location);
});

const uniqueMaterialsWithInfo = Object.keys(uniqueMaterials).map(
  (material) => ({
    Material: material,
    Description: uniqueMaterials[material].Description,
    ProductCode: uniqueMaterials[material].ProductCode,
    Locations: uniqueMaterials[material].Locations,
  })
);

function isMaterialFound(array, materialCode) {
  for (let i = 0; i < array.length; i++) {
    if (array[i].Material === materialCode) {
      return true; // Material found
    }
  }
  return false; // Material not found
}

const onAddMaterial = (item) => {
  model.value = false;
  item.sumOfQuantity = 0;
  emit("onAddMaterial", item);
};
</script>

<style></style>
