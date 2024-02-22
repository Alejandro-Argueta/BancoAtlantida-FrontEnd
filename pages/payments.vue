<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8">
      <v-card>
        <v-card-title class="headline">
          Registro de Pago
        </v-card-title>
        <v-card-text>
          <div>
            <h2>Cliente: {{ customerName }} {{ customerlastName }}</h2>
            <p>Número de Tarjeta: {{ creditCardNumber }} </p>
          </div>
          <v-form ref="form" @submit.prevent="submitForm">
            <v-row>
              <v-col cols="12">
                <v-menu
                  v-model="dateMenu"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template #activator="{ on, attrs }">
                    <v-text-field
                      v-model="purchaseDate"
                      label="Fecha de Pago"
                      required
                      readonly
                      v-bind="attrs"
                      outlined
                      dense
                      v-on="on"
                    />
                  </template>
                  <v-date-picker
                    v-model="purchaseDate"
                    no-title
                    scrollable
                    @input="dateMenu = false"
                  />
                </v-menu>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  v-model="amount"
                  label="Monto"
                  required
                  :rules="[positiveNumber]"
                  outlined
                  dense
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6">
                <v-btn color="primary" @click="save()">
                  Guardar
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn color="error" @click="cancel()">
                  Cancelar
                </v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Buys',
  data () {
    return {
      customerName: '',
      customerlastName: '',
      creditCardNumber: '',
      purchaseDate: '',
      description: 'Pago de TC',
      dateMenu: false,
      amount: '',
      transactionId: 2,
      creditCardId: this.$route.params.id,
      positiveNumber: value =>
        /^\d+(\.\d+)?$/.test(value) && parseFloat(value) >= 0
    }
  },
  beforeMount () {
    const creditCardId = this.$route.params.id
    if (creditCardId) {
      axios
        .get(`https://localhost:44310/api/CreditCard/${creditCardId}`)
        .then((response) => {
          const data = response.data
          this.creditCardNumber = data.number
          this.customerName = data.user.name
          this.customerlastName = data.user.lastName
        })
        .catch((error) => {
          console.error(
            'Error fetching customer and credit card information:',
            error
          )
        })
    } else {
      console.error('Credit card ID is undefined.')
      this.$router.push({ name: 'card' })
    }
  },
  methods: {
    save () {
      console.log('Guardar método llamado')
      const payload = {
        date: this.purchaseDate,
        description: this.description,
        amount: parseFloat(this.amount)
      }
      console.log('data', this.purchaseDate, 'des', this.description, 'amo', this.amount, 'id1', this.transactionId, 'id2', this.creditCardId)
      axios
        .post(`https://localhost:44310/api/Transaction?transactionId=${this.transactionId}&creditCardId=${this.creditCardId}`, payload)
        .then((response) => {
          console.log('Compra guardada:', response.data)
          this.$router.push({ name: 'card' })
        })
        .catch((error) => {
          console.error('Error saving purchase:', error)
        })
    },
    cancel () {
      this.$router.push({ name: 'card' })
    }
  }
}
</script>
