<template>

  <q-page style="background-color: #F6F8FC;">

    <q-tab-panels v-model="tab" animated>
      <q-tab-panel name="tabCars" class="no-padding" style="background-color: #F6F8FC;">
        <PageCars @onClick="view"/>
      </q-tab-panel>

      <q-tab-panel name="tabSimulation" class="no-padding" style="background-color: #F6F8FC;">
        <PageSimulation :grafoValues="grafoValues" @returnTab="returnTab"/>
      </q-tab-panel>
    </q-tab-panels>

  </q-page>

  <q-dialog v-model="calcModal">
    <modalCalcVertices :carAutonomy="carAutonomy" @nextPage="nextTab" @grafoValues="grafoProps"/>
  </q-dialog>

</template>
<script setup>
import { ref } from 'vue'

import PageCars from 'src/pages/CarsPage.vue'
import PageSimulation from 'src/pages/PageSimulation.vue'
import modalCalcVertices from 'src/components/modalCalcVertices.vue'

const tab = ref('tabCars')

const calcModal = ref(false)
const carAutonomy = ref(null)

const grafoValues = ref([])

const view = (value) => {
  calcModal.value = true
  carAutonomy.value = value
}

const nextTab = () => {
  tab.value = 'tabSimulation'
}

const returnTab = () => {
  tab.value = 'tabCars'
}

const grafoProps = (value) => {
  grafoValues.value = value
}

</script>
