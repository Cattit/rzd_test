<template>
  <div id="app">
    <v-app>
      <v-card-title>
        <v-text-field v-model="search" label="Search" single-line hide-details></v-text-field>
      </v-card-title>
      <v-data-table
        @click:row="editItem"
        :headers="headers"
        :items="data"
        :items-per-page="10"
        :search="search"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-dialog v-model="dialog" max-width="500px">
            <v-card>
              <v-card-title>
                <span class="headline">Изменить данные</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6">
                      <p>Статус: {{editedItem.carStatus}}</p>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <p>Номер вагона: {{editedItem.carNumber}}</p>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <p>stateId: {{editedItem.stateId}}</p>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-text-field v-model="editedItem.trainNumber" label="Номер поезда"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-text-field v-model="editedItem.lastOperDt" label="Дата-время"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-text-field v-model="editedItem.invoiceNumber" label="№ накладной"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-text-field v-model="editedItem.invoiceId" label="ИД накладной"></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">Отмена</v-btn>
                <v-btn color="blue darken-1" text @click="save">OK</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </template>
        <template v-slot:item.lastOperDt="{ item }">{{ formatDate(item.lastOperDt) }}</template>
      </v-data-table>
    </v-app>
  </div>
</template>

<script>
import data from "./table-data.json";

export default {
  name: "App",
  data() {
    return {
      selected: [],
      search: "",
      dialog: false,
      editedIndex: -1,
      editedItem: {
        lastOperDt: "",
        trainNumber: 0,
        invoiceNumber: 0,
        invoiceId: 0
      },
      defaultItem: {
        lastOperDt: "",
        trainNumber: 0,
        invoiceNumber: 0,
        invoiceId: 0
      },
      headers: [
        {
          text: "№ n/n",
          sortable: true,
          value: "ordNumber"
        },
        { text: "Номер вагона", value: "carNumber" },
        { text: "Номер поезда", value: "trainNumber" },
        { text: "Статус", value: "carStatus" },
        { text: "Дата-время операции", value: "lastOperDt" },
        { text: "№ накладной", value: "invoiceNumber" },
        { text: "ИД накладной", value: "invoiceId" },
        { text: "stateId", value: "stateId" }
      ],
      data
    };
  },
  watch: {
    dialog(val) {
      val || this.close();
    }
  },
  methods: {
    editItem(item) {
      this.editedIndex = this.data.indexOf(item);
      this.editedItem = { ...item };
      this.dialog = true;
    },
    formatDate(date) {
      if (date == null) return "–";
      return new Date(date).toLocaleString("ru-RU");
    },
    close() {
      this.dialog = false;
      this.editedItem = { ...this.defaultItem };
    },
    save() {
      Object.assign(this.data[this.editedIndex], this.editedItem);
      this.close();
    }
  }
};
</script>

<style>
</style>
