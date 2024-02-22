<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8">
      <v-card>
        <v-card-title class="headline">
          Listado de Tarjetas
        </v-card-title>
        <v-card-text>
          <v-data-table :items="creditCards" :headers="headers">
            <template v-slot:item="{ item }">
              <tr>
                <td>{{ item.number }}</td>
                <td>{{ item.user.name }}</td>
                <td>{{ item.user.lastName }}</td>
                <td>
                  <v-tooltip bottom>
                    <template #activator="{ on }">
                      <v-btn small color="primary" @click="navigateToStatusPage(item.id)" v-on="on">
                        <v-icon>mdi-eye</v-icon>
                      </v-btn>
                    </template>
                    <span>Ver Estado de Cuenta</span>
                  </v-tooltip>
                  <v-tooltip bottom>
                    <template #activator="{ on }">
                      <v-btn small color="info" @click="registerPurchase(item.id)" v-on="on">
                        <v-icon>mdi-cart-plus</v-icon>
                      </v-btn>
                    </template>
                    <span>Registrar Compra</span>
                  </v-tooltip>
                  <v-tooltip bottom>
                    <template #activator="{ on }">
                      <v-btn small color="success" @click="registerPayment(item.id)" v-on="on">
                        <v-icon>mdi-currency-usd</v-icon>
                      </v-btn>
                    </template>
                    <span>Registrar Pago</span>
                  </v-tooltip>
                  <v-tooltip bottom>
                    <template #activator="{ on }">
                      <v-btn small color="warning" @click="viewTransactions(item.id)" v-on="on">
                        <v-icon>mdi-history</v-icon>
                      </v-btn>
                    </template>
                    <span>Ver Transacciones</span>
                  </v-tooltip>
                </td>
              </tr>
            </template>
          </v-data-table>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Card',
  data () {
    return {
      creditCards: [],
      headers: [
        { text: 'Número de Tarjeta', value: 'number' },
        { text: 'Nombre', value: 'user.name' },
        { text: 'Apellido', value: 'user.lastName' },
        { text: 'Acciones' }
      ]
    }
  },
  created () {
    this.fetchCreditCards()
  },
  methods: {
    fetchCreditCards () {
      axios.get('https://localhost:44310/api/CreditCard')
        .then((response) => {
          this.creditCards = response.data
        })
        .catch((error) => {
          console.error('Error fetching credit cards:', error)
        })
    },
    navigateToStatusPage (creditCardId) {
      this.$router.push({ name: 'status', params: { id: creditCardId } })
    },
    registerPurchase (creditCardId) {
      this.$router.push({ name: 'buys', params: { id: creditCardId } })
    },
    registerPayment (creditCardId) {
      this.$router.push({ name: 'payments', params: { id: creditCardId } })
    },
    viewTransactions (creditCardId) {
      this.$router.push({ name: 'transactions', params: { id: creditCardId } })
    }
  }
}
</script>
