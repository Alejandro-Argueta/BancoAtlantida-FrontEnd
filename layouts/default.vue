<template>
  <v-app dark>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
        <v-snackbar
          v-model="snackbar.show"
          :color="snackbar.color"
          :timeout="snackbar.timeout"
          top
        >
          {{ snackbar.message }}
          <template v-if="snackbar.showClose">
            <v-btn color="white" text @click="snackbar.show = false">
              Cerrar
            </v-btn>
          </template>
        </v-snackbar>
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ title }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      snackbar: {
        show: false,
        message: '',
        color: '',
        timeout: 3000,
        showClose: true
      },
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Banco Atlantida'
    }
  },
  mounted () {
    this.$root.$on('show-snackbar', (message, color) => {
      this.snackbar.message = message
      this.snackbar.color = color
      this.snackbar.show = true
    })
  }
}
</script>
