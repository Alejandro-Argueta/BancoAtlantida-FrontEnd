<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline">
          Sistema de Estados de Cuenta
        </v-card-title>
        <v-card-text class="headline">
          <h6>Prueba Tecnica: Banco Atlantida</h6>
        </v-card-text>
        <v-card-text>
          <p><b>Nombre: </b>Santos Alejandro Argueta Noyola</p>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/card"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>
