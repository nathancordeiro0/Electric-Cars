<template>

  <q-page style="background-color: #F6F8FC;">

    <div class="q-pa-md">

      <q-toolbar class="q-px-none q-mb-md">
        <q-input v-model="filterString" class="col-2 q-px-sm q-mr-md bg-white borda-redonda" borderless dense placeholder="Pesquisar modelo..." @keyup.esc.exact="filterString = ''">
          <template v-slot:prepend>
            <q-icon v-if="!filterString.length" color="grey-8" name="search"/>
            <q-icon v-else class="cursor-pointer" color="grey-8" name="close" @click="filterString = ''"/>
          </template>
        </q-input>

        <q-select v-model="filterBrand" :options="options" clearable class="col-2 q-px-sm bg-white borda-redonda" borderless dense label="Filtrar por marca" color="grey-8" popup-content-style="border-radius: 7.5px" @change="updateSelectOption()"/>

        <q-space/>

        <div class="text-caption text-grey-8 q-pr-sm">Página: {{ currentPage }} de {{ totalPages }}</div>
        <q-btn flat round dense size="12px" color="grey-8" icon="chevron_left" @click="previousPage()"/>
        <q-btn flat round dense size="12px" color="grey-8" icon="chevron_right" @click="nextPage()"/>
      </q-toolbar>

      <div class="row q-col-gutter-md">

        <div v-for="(car, index) in paginatedCars" :key="index" class="col-3">
          <q-card class="borda-redonda zoom-card cursor-pointer" flat @click="emitEvent(car.autonomy)">
            <q-card-section class="q-pa-sm">

              <div class="q-mb-sm">
                <q-img class="borda-redonda" :src="car.source" style="height: 250px;"/>
              </div>

              <q-item>
                <q-item-section>
                  <q-item-label class="text-subtitle1 text-weight-medium text-primary">{{ car.brand }}</q-item-label>

                  <q-item-label class="text-subtitle2 text-weight-medium text-grey-8">{{ car.model }}</q-item-label>

                  <q-item-label class="text-body2 text-weight-medium text-grey-8">Autonomia: {{ car.autonomy }} KM</q-item-label>

                </q-item-section>
              </q-item>

            </q-card-section>
          </q-card>
        </div>

        <div v-if="paginatedCars.length === 0 && filterString !== ''" class="fit text-center text-subtitle1 text-weight-medium text-grey-8 q-py-md">
          Nenhum resultado encontrado.
        </div>
      </div>

    </div>

  </q-page>

</template>

<script setup>
import axios from 'axios'
import { computed, onBeforeMount, ref, watch } from 'vue'

const emit = defineEmits(['onClick', 'carAutonomy'])

const cars = ref([])
const pageSize = ref(8)
const currentPage = ref(1)

const filterString = ref('')
const filterBrand = ref(null)
const options = ref([])

const getCars = () => {
  axios.get('src/data/cars.json')
    .then(response => {
      cars.value = response.data.data
      options.value = response.data.data
        .map(item => item.brand)
        .filter((value, index, self) => self.indexOf(value) === index)
    })
}

const filteredCars = computed(() => {
  const filter = filterString.value.toLowerCase()
  let filtered = cars.value.filter(car => car.model.toLowerCase().includes(filter))

  if (filterBrand.value) {
    const selectedBrand = filterBrand.value.toLowerCase()
    filtered = filtered.filter(car => car.brand.toLowerCase() === selectedBrand)
  }

  return filtered
})

const totalPages = computed(() => {
  return Math.ceil(filteredCars.value.length / pageSize.value)
})

const paginatedCars = computed(() => {
  const startIndex = (currentPage.value - 1) * pageSize.value
  return filteredCars.value.slice(startIndex, startIndex + pageSize.value)
})

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++
  }
}

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
  }
}

const emitEvent = (value) => {
  emit('onClick', value)
}

watch(filteredCars, () => {
  currentPage.value = 1
})

onBeforeMount(() => {
  getCars()
})

</script>
