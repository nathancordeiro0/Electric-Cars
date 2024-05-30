<template>

  <div class="borda-redonda tamanho-modal">

    <q-card class="borda-redonda" style="background-color: #F6F8FC;">
      <q-card-section class="q-pa-sm">

        <q-toolbar>
          <q-toolbar-title class="text-subtitle1 text-weight-medium text-grey-8">Cálculo dos pontos de recarga necessários</q-toolbar-title>

          <q-btn flat round dense size="12.5px" color="grey-8" icon="close" v-close-popup>
            <q-tooltip>
              Fechar
            </q-tooltip>
          </q-btn>
        </q-toolbar>

        <div class="q-px-md q-my-sm q-col-gutter-y-sm">
          <div>
            <div class="q-pa-xs text-body2 text-weight-medium text-grey-8">Ponto de origem</div>
            <q-select
              class="q-px-sm bg-white borda-redonda"
              v-model="originPoint"
              :options="options"
              @filter="filterFn"
              menu-shrink
              borderless
              dense
              behavior
              hide-dropdown-icon
              :menu-offset="[0, 5]"
              use-input
              use-chips
              popup-content-class="borda-redonda altura-popup"
            >
              <template v-slot:no-option>
                <q-item>
                  <q-item-section class="text-caption text-grey-8 text-center">
                    Sem resultados
                  </q-item-section>
                </q-item>
              </template>
            </q-select>
          </div>

          <div>
            <div class="q-pa-xs text-body2 text-weight-medium text-grey-8">Ponto de destino</div>
            <q-select
              class="q-px-sm bg-white borda-redonda"
              v-model="destinationPoint"
              :options="options"
              @filter="filterFn"
              menu-shrink
              borderless
              dense
              behavior
              hide-dropdown-icon
              :menu-offset="[0, 5]"
              use-input
              use-chips
              popup-content-class="borda-redonda"
            >
              <template v-slot:no-option>
                <q-item>
                  <q-item-section class="text-caption text-grey-8 text-center">
                    Sem resultados
                  </q-item-section>
                </q-item>
              </template>
            </q-select>
          </div>

          <div>
            <div class="q-pa-xs text-body2 text-weight-medium text-grey-8">Distância entre os pontos (KM)</div>
            <q-input class="q-px-sm bg-white borda-redonda" v-model="distanceBetweenPoints" borderless dense/>
          </div>
        </div>

        <div v-if="result === true" class="q-ma-lg q-py-sm text-center bg-white borda-redonda">
          <div class="text-subtitle2 text-weight-medium text-grey-8">
            Entre <span class="text-weight-bold text-grey-9">{{ originPoint }}</span> e <span class="text-weight-bold text-grey-9">{{ destinationPoint }}</span>
          </div>

          <div class="text-subtitle2 text-weight-medium text-grey-8">
            você precisará recarregar em <span class="text-primary text-weight-bold">{{ points }}</span> ponto(s) de recarga.
          </div>

          <div class="q-mt-sm">
            <q-btn class="text-primary" round dense size="12.5px" icon="o_visibility" @click="emitEvent" v-close-popup>
              <q-tooltip>
                Visualizar simulação
              </q-tooltip>
            </q-btn>
          </div>
        </div>

        <div class="q-px-md q-gutter-x-sm q-mt-lg text-right">
          <q-btn class="q-px-sm text-primary borda-redonda" flat dense no-caps label="Cancelar" v-close-popup/>
          <q-btn class="q-px-sm text-primary borda-redonda" flat dense no-caps label="Calcular" @click="calcDistance"/>
        </div>

      </q-card-section>
    </q-card>

  </div>

</template>
<script setup>
import axios from 'axios'
import { onBeforeMount, ref } from 'vue'
import { useQuasar } from 'quasar'

const q = useQuasar()
const props = defineProps(['carAutonomy'])
const emit = defineEmits(['nextPage', 'grafoValues'])

const states = ref([])

const originPoint = ref(null)
const destinationPoint = ref(null)
const stringOptions = ref([])
const options = ref([])

const distanceBetweenPoints = ref(null)

const points = ref(null)
const result = ref(false)

const getStates = () => {
  axios.get('src/data/states.json')
    .then(response => {
      states.value = response.data.data
      stringOptions.value = response.data.data.map(item => item.name)
      options.value = stringOptions.value
    })
}

const calcDistance = () => {
  if (verify(originPoint.value, 'O campo ponto de origem não pode estar em branco')) return true

  if (verify(destinationPoint.value, 'O campo ponto de destino não pode estar em branco')) return true

  if (verify(distanceBetweenPoints.value, 'O campo de distância entre os pontos não pode estar em branco')) return true

  result.value = true
  points.value = (parseInt(distanceBetweenPoints.value) / props.carAutonomy).toFixed(0)
}

const verify = (field, message) => {
  if (field === undefined || field === null || field === '' || field.length === 0) {
    q.notify({ type: 'negative', color: 'red', message, position: 'top-right' })
    return true
  }
  return false
}

const filterFn = (val, update) => {
  update(() => {
    const needle = val.toLowerCase()
    options.value = stringOptions.value.filter(v => v.toLowerCase().indexOf(needle) > -1)
  })
}

const emitEvent = () => {
  emit('grafoValues', {
    originPoint: originPoint.value,
    destinationPoint: destinationPoint.value,
    points: parseInt(points.value)
  })

  emit('nextPage')
}

onBeforeMount(() => {
  getStates()
})

</script>
