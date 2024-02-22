<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8">
      <v-card>
        <v-card-title class="headline">
          Transacciones
        </v-card-title>
        <v-card-text>
          <div>
            <h2>Cliente: {{ customerName }}</h2>
            <p>Número de Tarjeta de Crédito: {{ creditCardNumber }}</p>
          </div>
          <v-data-table :items="transactions" :headers="headers">
            <template #item="{ item }">
              <tr>
                <td>{{ item.id }}</td>
                <td>{{ formatDateString(item.date) }}</td>
                <td>{{ item.description }}</td>
                <td>{{ item.transactionType.name === 'Compra' ? item.amount.toFixed(2) : '' }}</td>
                <td>{{ item.transactionType.name === 'Pago' ? item.amount.toFixed(2) : '' }}</td>
              </tr>
            </template>
          </v-data-table>
          <v-btn color="primary" @click="goBack">Volver</v-btn>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Transactions',
  data () {
    return {
      customerName: '',
      creditCardNumber: '',
      transactions: [],
      headers: [
        { text: 'Número de Autorización', value: 'id' },
        { text: 'Fecha', value: 'date' },
        { text: 'Descripción', value: 'description' },
        { text: 'Cargo', value: 'amount' },
        { text: 'Abono', value: 'amount' }
      ]
    }
  },
  mounted () {
    const creditCardId = this.$route.params.id
    if (creditCardId) {
      axios.get(`https://localhost:44310/api/CreditCard/${creditCardId}`)
        .then((response) => {
          this.customerName = response.data.user.name + ' ' + response.data.user.lastName
          this.creditCardNumber = response.data.number
          this.transactions = response.data.transactions
        })
        .catch((error) => {
          console.error('Error al obtener datos de la tarjeta de crédito:', error)
        })
    } else {
      this.$router.push({ name: 'card' })
    }
  },
  methods: {
    goBack () {
      this.$router.push({ name: 'card' })
    },
    formatDateString (dateString) {
      const year = dateString.slice(0, 4)
      const month = dateString.slice(5, 7)
      const day = dateString.slice(8, 10)
      return `${day}/${month}/${year}`
    }
  }
}
</script>
