<template>
  <div>
    <v-row justify="center">
      <v-col cols="12" sm="10" md="8">
        <v-card>
          <v-card-title class="headline">
            Estado de Cuenta de TC
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" md="12">
                <div>
                  <h2>Cliente: {{ customerName }}</h2>
                  <p>Número de Tarjeta: {{ creditCardNumber }}</p>
                </div>
              </v-col>
              <v-col cols="6" md="6">
                <div>
                  <p><b>Saldo Actual:</b> ${{ currentBalance.toFixed(2) }}</p>
                  <p><b>Limite:</b> ${{ limit.toFixed(2) }}</p>
                  <p><b>Interes Bonificable:</b> ${{ bonifiableInterest.toFixed(2) }}</p>
                  <p><b>Saldo Disponible:</b> ${{ (limit - currentBalance).toFixed(2) }}</p>
                </div>
              </v-col>
              <v-col cols="6">
                <div>
                  <p><b>Cuota Mínima a Pagar:</b> ${{ (currentBalance * (percentageMinimumBalance / 100)).toFixed(2) }}</p>
                  <p><b>Monto total a Pagar:</b> ${{ currentBalance.toFixed(2) }}</p>
                  <p><b>Monto total de Contado con Intereses:</b> ${{ (currentBalance + bonifiableInterest).toFixed(2) }}</p>
                </div>
              </v-col>
            </v-row>

            <v-divider class="my-4" />

            <v-data-table :items="transactions" :headers="headers">
              <template #item="{ item }">
                <tr>
                  <td v-show="item.transactionType.id === 1">{{ item.id }}</td>
                  <td v-show="item.transactionType.id === 1">{{ formatDateString(item.date) }}</td>
                  <td v-show="item.transactionType.id === 1">{{ item.description }}</td>
                  <td v-show="item.transactionType.id === 1">${{ item.amount.toFixed(2) }}</td>
                </tr>
              </template>
            </v-data-table>

            <h3>Compras del Mes Anterior</h3>
            <p>Total: ${{ previousMonthTotal }}</p>
            <h3>Compras de Este Mes</h3>
            <p>Total: ${{ currentMonthTotal }}</p>

            <v-btn color="primary" @click="goBack">
              Volver
            </v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Status',
  data () {
    return {
      bonifiableInterest: 0,
      currentBalance: 0,
      customerName: '',
      interestPercentage: 0,
      percentageMinimumBalance: 0,
      creditCardNumber: '',
      transactions: [],
      limit: 0,
      previousMonthTotal: null,
      currentMonthTotal: null,
      headers: [
        { text: 'Número de Autorización', value: 'id' },
        { text: 'Fecha', value: 'date' },
        { text: 'Descripción', value: 'description' },
        { text: 'Monto', value: 'amount' }
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
          this.limit = response.data.limit
          this.interestPercentage = response.data.interestPercentage
          this.percentageMinimumBalance = response.data.percentageMinimumBalance
          this.transactions = response.data.transactions
          const today = new Date()
          const currentMonth = today.getMonth() + 1
          const currentYear = today.getFullYear()
          const previousMonth = currentMonth === 1 ? 12 : currentMonth - 1
          const previousYear = currentMonth === 1 ? currentYear - 1 : currentYear

          this.previousMonthTotal = this.calculateTotalForMonth(previousMonth, previousYear)
          this.currentMonthTotal = this.calculateTotalForMonth(currentMonth, currentYear)
          this.currentBalance = this.calculateCurrentBalance()
          this.bonifiableInterest = this.currentBalance * (this.interestPercentage / 100)
        })
        .catch((error) => {
          console.error('Error al obtener datos de la tarjeta de crédito:', error)
        })
    } else {
      this.$router.push({ name: 'card' })
    }
  },
  methods: {
    calculateCurrentBalance () {
      const purchases = this.transactions.filter(transaction => transaction.transactionType.name === 'Compra')
      return purchases.reduce((total, purchase) => total + purchase.amount, 0)
    },
    formatDateString (dateString) {
      const year = dateString.slice(0, 4)
      const month = dateString.slice(5, 7)
      const day = dateString.slice(8, 10)
      return `${day}/${month}/${year}`
    },
    goBack () {
      this.$router.push({ name: 'card' })
    },
    calculateTotalForMonth (month, year) {
      const transactionsForMonth = this.transactions.filter((transaction) => {
        const transactionDate = new Date(transaction.date)
        return transactionDate.getMonth() + 1 === month && transactionDate.getFullYear() === year && transaction.transactionType.name === 'Compra'
      })

      return transactionsForMonth.reduce((total, transaction) => total + transaction.amount, 0)
    }
  }
}
</script>
