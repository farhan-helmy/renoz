<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="services"
      :search="search"
      sort-by="created_at"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title> Services </v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>

          <v-spacer></v-spacer>
          <v-btn color="primary" dark @click="serviceForm = true">
            Add Service
          </v-btn>
        </v-toolbar>
        <v-dialog v-model="serviceForm">
          <v-card>
            <v-card-title>
              <span class="text-h5">Add Service</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="6">
                      <v-text-field
                      label="Service Name"
                      required
                      v-model="service.service_name"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      label="Description "
                      required
                      v-model="service.description"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="serviceForm = false">
                Close
              </v-btn>
              <v-btn color="blue darken-1" text @click="addService">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon medium @click="viewItem(item)"> mdi-eye </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}

      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  data: () => ({
    snackbar: false,
    text: 'Property created!',
    timeout: 2000,
    serviceForm: false,
    search: '',
    headers: [
      {
        text: 'Service Name',
        align: 'start',
        sortable: false,
        value: 'service_name',
      },
      {
        text: 'Created At',
        align: 'start',
        sortable: false,
        value: 'createdAt',
      },
      {
        text: 'Updated At',
        align: 'start',
        sortable: false,
        value: 'updatedAt',
      },
      { text: 'Actions', value: 'actions', sortable: false },
    ],
    services: [],
    states: [],
    service: [],
    formData: {
      name: '',
      email: '',
      phone_no: '',
      zip_code: '',
      state: '',
      city: '',
      street_address: '',
      user_id: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    async initialize() {
      let results = await this.$axios.get('/v1/services')
      console.log(results.data)
      this.services = results.data
    },
    async addService() {
      console.log(this.service)
      const send = {
        service_name: this.service.service_name,
        description: this.service.description
      }
      let response = await this.$axios.post('/v1/service', send)
      console.log(response)
      if (response.status === 201) {
        this.serviceForm = false
        this.snackbar = true
      }
    },
    viewItem(item) {
      this.$router.push(`/admin/services/${item._id}`)
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },
    getColor(status) {
      if (status === 'active') return 'green'
    },
  },
}
</script>