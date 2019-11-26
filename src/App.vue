<template>
  <div id="app">
    <v-app>
      <v-card-title>
        <v-text-field v-model="search" label="Search" single-line hide-details></v-text-field>
      </v-card-title>
      <v-data-table
        @click:row="editItem"
        :headers="headers"
        :items="items"
        :items-per-page="10"
        :search="search"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-dialog v-model="dialog" max-width="500px">
            <v-form ref="form" v-model="valid" lazy-validation>
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
                        <v-text-field
                          :rules="dateRules"
                          v-model="editedItem.lastOperDt"
                          label="Дата-время"
                        ></v-text-field>
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
                  <v-btn color="blue darken-1" :disabled="!valid" text @click="save">OK</v-btn>
                </v-card-actions>
              </v-card>
            </v-form>
          </v-dialog>
        </template>
      </v-data-table>
    </v-app>
  </div>
</template>

<script>
import testData from "./table-data.json";
import moment from "moment";

export default {
  name: "App",
  // начальные данные
  data() {
    return {
      valid: true,
      // проверка формата даты
      dateRules: [
        v =>
          v === "-" ||
          moment(v, "DD.MM.YYYY HH:mm:ss", true).isValid() ||
          "Date must be valid"
      ],
      search: "",
      dialog: false,
      editedIndex: -1,
      editedItem: {
        lastOperDt: "",
        trainNumber: 0,
        invoiceNumber: 0,
        invoiceId: 0
      },
      // заголовки
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
      // тело таблицы
      items: this.formatAllDates(testData)
    };
  },
  watch: {
    dialog(val) {
      val || this.close();
    }
  },
  methods: {
    editItem(item) {
      this.editedIndex = this.items.indexOf(item);
      this.editedItem = { ...item };
      this.dialog = true;
    },
    // приведение даты к нужному формату
    formatDate(date) {
      if (!date) return "-";
      return moment(date).format("DD.MM.YYYY HH:mm:ss");
    },
    formatAllDates(items) {
      return items.map(item => ({
        ...item,
        lastOperDt: this.formatDate(item.lastOperDt)
      }));
    },
    // работа с модальным окном
    close() {
      this.dialog = false;
      this.editedItem = {
        lastOperDt: "",
        trainNumber: 0,
        invoiceNumber: 0,
        invoiceId: 0
      };
    },
    validate() {
      if (this.$refs.form.validate()) {
        this.snackbar = true;
      }
    },
    save() {
      this.validate();
      this.items = this.items.map((item, index) =>
        index === this.editedIndex ? this.editedItem : item
      );
      this.close();
    }
  }
};
</script>

<style>
</style>
