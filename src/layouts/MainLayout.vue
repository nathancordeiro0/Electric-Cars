<template>
  <q-layout view="hhh LpR fFf">

    <q-header class="q-pa-sm" style="background-color: #F6F8FC;">
      <q-toolbar class="bg-white q-py-md borda-redonda">
        <q-toolbar-title class="text-subtitle1 text-weight-medium text-grey-8">Cálculo sobre carros elétricos</q-toolbar-title>

        <q-space/>

        <q-btn class="text-grey-8" round dense icon="sym_o_download" @click="downloadExcel()">
          <q-tooltip>
            Fazer download do planilha
          </q-tooltip>
        </q-btn>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

  </q-layout>
</template>

<script setup>

const downloadExcel = async () => {
  const fileUrl = 'src/assets/planilha_de_automacao.xlsx'

  try {
    const response = await fetch(fileUrl)
    const blob = await response.blob()
    const url = window.URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = 'planilha_de_automacao.xlsx'
    document.body.appendChild(link)
    link.click()
    document.body.removeChild(link)
    window.URL.revokeObjectURL(url)
  } catch (error) {
    console.error(error)
  }
}

</script>
