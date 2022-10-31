<template>
  <div class="text-center">
    <v-dialog
      v-model="edit_property_dialog"
      :width="$vuetify.breakpoint.xs ? '90vw' : '40vw'"
      scrollable
      persistent
    >
      <v-card>
        <v-card-title
          class="d-flex justify-space-between text-h5 primary white--text"
        >
          Edit Property
          <v-icon @click="closeModal" color="white">mdi-close</v-icon>
        </v-card-title>

        <v-card-text class="pa-5">
          <ValidationObserver ref="observer" v-slot="{ valid, invalid }">
            <form class="d-flex flex-column" @submit.prevent="onSubmit">
              <v-row>
                <v-col class="my-0 py-0" cols="12">
                  <Input
                    :valid="valid"
                    title="Type"
                    name="type"
                    type="radio"
                    rules="required"
                    :options="type"
                    v-if="form.type"
                    :value="form.type"
                    @model="model($event, 'type')"
                  />
                </v-col>
                <v-col class="my-0 py-0" cols="12">
                  <Input
                    :valid="valid"
                    title="Property Name"
                    name="property_name"
                    type="combobox"
                    rules="required"
                    :options="item.options"
                    v-if="form.property_name"
                    :value="form.property_name"
                    @model="model($event, 'property_name')"
                  />
                </v-col>
                <v-col class="my-0 py-0" cols="12">
                  <Input
                    :valid="valid"
                    title="Purchaser"
                    name="purchaser"
                    type="radio"
                    rules="required"
                    :options="purchaser"
                    v-if="form.purchaser"
                    :value="form.purchaser"
                    @model="model($event, 'purchaser')"
                  />
                </v-col>
                <v-col class="my-0 py-0" cols="12">
                  <Input
                    :valid="valid"
                    title="Property Code"
                    name="property_code"
                    type="text"
                    rules="required"
                    v-if="form.property_code"
                    :value="form.property_code"
                    @model="model($event, 'property_code')"
                  />
                </v-col>
                <v-col cols="12" md="6" sm="12" xs="12">
                  <Input
                    :valid="valid"
                    title="Category"
                    name="category"
                    type="autocomplete"
                    rules="required"
                    item_text="name"
                    item_value="id"
                    :hasIcon="category.hasIcon"
                    :options="category.options"
                    v-if="form.category"
                    :value="form.category"
                    @model="model($event, 'category')"
                    @modalInput="addCategory"
                  />
                </v-col>
                <v-col cols="12" md="6" sm="12" xs="12">
                  <Input
                    :valid="valid"
                    title="Description"
                    row="1"
                    name="description"
                    type="textarea"
                    rules="required"
                    v-if="form.description"
                    :value="form.description"
                    @model="model($event, 'description')"
                  />
                </v-col>

                <v-col cols="12">
                  <ValidationProvider
                    v-slot="{ errors }"
                    :rules="
                      form.purchaser === 'Regional Office'
                        ? {
                            required: true,
                            regional_format: '^(ABCD - )[A-Za-z0-9]',
                          }
                        : { required: true }
                    "
                    name="Serial Number"
                  >
                    <v-text-field
                      class="pa-0 ma-0"
                      v-model="form.serial_number"
                      name="serial_number"
                      label="Serial Number"
                      placeholder="Serial Number"
                      type="text"
                      :error-messages="errors"
                      :success="valid"
                      v-if="form.serial_number"
                      :value="form.serial_number"
                    ></v-text-field>
                  </ValidationProvider>
                </v-col>

                <v-col cols="12">
                  <Input
                    :valid="valid"
                    title="Location"
                    name="location"
                    type="autocomplete"
                    rules="required"
                    item_text="name"
                    item_value="id"
                    :hasIcon="location.hasIcon"
                    :options="location.options"
                    v-if="form.location"
                    :value="form.location"
                    @model="model($event, 'location')"
                    @modalInput="addLocation"
                  />
                </v-col>
                <v-col cols="12">
                  <Input
                    :valid="valid"
                    title="Received By"
                    name="received_by"
                    type="autocomplete"
                    rules="required"
                    item_text="full_name"
                    item_value="id"
                    :hasIcon="received_by.hasIcon"
                    :options="received_by.options"
                    v-if="form.received_by"
                    :value="form.received_by"
                    @model="model($event, 'received_by')"
                    @modalInput="addEmployee"
                  />
                </v-col>
                <v-col cols="12">
                  <Input
                    :valid="valid"
                    title="Received From"
                    name="received_from"
                    type="autocomplete"
                    rules="required"
                    item_text="full_name"
                    item_value="id"
                    :options="received_by.options"
                    v-if="form.received_from"
                    :value="form.received_from"
                    @model="model($event, 'received_from')"
                  />
                </v-col>
                <v-col cols="12">
                  <Input
                    :valid="valid"
                    title="Assigned Person"
                    name="assigned_person"
                    type="autocomplete"
                    rules="required"
                    item_text="full_name"
                    item_value="id"
                    :options="assigned_person.options"
                    v-if="form.assigned_person"
                    :value="form.assigned_person"
                    @model="model($event, 'assigned_person')"
                  />
                </v-col>
                <v-col cols="12" lg="4" sm="12" xs="12">
                  <Input
                    :valid="valid"
                    title="Status"
                    name="status"
                    type="autocomplete"
                    rules="required"
                    item_text="name"
                    item_value="id"
                    :options="status.options"
                    v-if="form.status"
                    :value="form.status"
                    @model="model($event, 'status')"
                  />
                </v-col>
                <v-col cols="12" lg="4" md="4" sm="6" xs="12">
                  <Input
                    :valid="valid"
                    title="Date Acquired"
                    name="date_acquired"
                    type="date"
                    rules="required"
                    v-if="form.date_acquired"
                    :value="String(form.date_acquired)"
                    @model="model($event, 'date_acquired')"
                  />
                </v-col>
                <v-col cols="12" lg="4" md="4" sm="6" xs="12">
                  <Input
                    :valid="valid"
                    title="Date Received"
                    name="date_received"
                    type="date"
                    rules="required"
                    v-if="form.date_received"
                    :value="form.date_received"
                    @model="model($event, 'date_received')"
                  />
                </v-col>
                <v-col cols="12" lg="6" sm="12" xs="12">
                  <Input
                    :valid="valid"
                    title="Cost"
                    name="cost"
                    type="number"
                    rules="required"
                    @model="model($event, 'cost')"
                  />
                </v-col>
                <v-col cols="12" lg="6" sm="12" xs="12">
                  <Input
                    :valid="valid"
                    title="Quantity"
                    name="quantity"
                    type="number"
                    rules="required"
                    @model="model($event, 'quantity')"
                  />
                </v-col>
                <v-col v-if="computeCost > 0" cols="12">
                  <v-alert type="success">
                    {{ $convertToCurrency(computeCost) }}
                  </v-alert>
                </v-col>
              </v-row>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn type="submit" color="primary" :disabled="invalid">
                  Add Property

                  <v-icon class="mx-1"> mdi-close</v-icon>
                </v-btn>
              </v-card-actions>
            </form>
          </ValidationObserver>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  props: {
    edit_property_dialog: Boolean,
    edit_data: Object,
  },
  data: () => ({
    form: {
      serial_number: null,
      purchaser: null,
      cost: 0,
      quantity: 0,
    },
    item: {
      options: [],
    },
    type: [
      { label: "Consumable", value: "Consumable" },
      { label: "Non-Consumable", value: "Non-Consumable" },
    ],
    purchaser: [
      {
        label: "Provincial Office",
        value: "Provincial Office",
      },
      { label: "Regional Office", value: "Regional Office" },
    ],
    category: {
      options: [],
      hasIcon: {
        status: true,
        icon: "mdi-map-marker-plus-outline",
        modal: {
          title: "Add Category",
          fields: [
            {
              cols: 1,
              name: "name",
              title: "Category",
              rules: "required",
              type: "text",
            },
          ],
          button: {
            color: "success",
            btn_name: "Save",
            icon: "mdi-cash-check",
          },
        },
      },
    },
    location: {
      options: [],
      hasIcon: {
        status: true,
        icon: "mdi-map-marker-plus-outline",
        modal: {
          title: "Location",
          fields: [
            {
              cols: 1,
              name: "name",
              title: "Location",
              rules: "required",
              type: "text",
            },
          ],
          button: {
            color: "success",
            btn_name: "Save",
            icon: "mdi-cash-check",
          },
        },
      },
    },
    received_by: {
      options: [],
      hasIcon: {
        status: true,
        icon: "mdi-map-marker-plus-outline",
        modal: {
          title: "Received By",
          fields: [
            {
              cols: 1,
              name: "fname",
              title: "First Name",
              rules: "required",
              type: "text",
            },
            {
              cols: 1,
              name: "mname",
              title: "Middle Name",
              type: "text",
            },
            {
              cols: 1,
              name: "lname",
              title: "Last Name",
              rules: "required",
              type: "text",
            },
            {
              cols: 1,
              name: "office",
              title: "Office",
              rules: "required",
              type: "text",
            },
          ],
          button: {
            color: "success",
            btn_name: "Save",
            icon: "mdi-cash-check",
          },
        },
      },
    },
    received_from: {
      options: [],
    },
    assigned_person: {
      options: [],
    },
    status: {
      options: [],
    },
  }),
  computed: {
    computeCost() {
      return this.form.quantity * this.form.cost;
    },
  },
  methods: {
    // Category API
    addCategory(form) {
      this.$store
        .dispatch("addCategory", form)
        .then(
          function (result) {
            this.category.options.push({
              id: result.id,
              name: result.get("name"),
            });
            this.$toast.success("New Category has been added successfully.");
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    getCategory() {
      this.$store
        .dispatch("getCategory")
        .then(
          function (result) {
            result.forEach((category) => {
              this.category.options.push({
                id: category.id,
                name: category.get("name"),
              });
            });
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    // End Category API

    // Location API
    addLocation(form) {
      this.$store
        .dispatch("addLocation", form)
        .then(
          function (result) {
            this.location.options.push({
              id: result.id,
              name: result.get("name"),
            });
            this.$toast.success("New Location has been added successfully.");
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    getLocation() {
      this.$store
        .dispatch("getLocation")
        .then(
          function (result) {
            result.forEach((location) => {
              this.location.options.push({
                id: location.id,
                name: location.get("name"),
              });
            });
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    // End Location API

    // Received By API
    addEmployee(form) {
      this.$store
        .dispatch("addEmployee", form)
        .then(
          function (result) {
            this.received_by.options.push({
              id: result.id,
              full_name: result?.get("fname") + " " + result?.get("lname"),
              fname: result?.get("fname"),
              mname: result?.get("mname"),
              lname: result?.get("lname"),
              office: result?.get("office"),
            });
            this.received_from.options.push({
              id: result.id,
              full_name: result?.get("fname") + " " + result?.get("lname"),
              fname: result?.get("fname"),
              mname: result?.get("mname"),
              lname: result?.get("lname"),
              office: result?.get("office"),
            });
            this.assigned_person.options.push({
              id: result.id,
              full_name: result?.get("fname") + " " + result?.get("lname"),
              fname: result?.get("fname"),
              mname: result?.get("mname"),
              lname: result?.get("lname"),
              office: result?.get("office"),
            });
            this.$toast.success("New Employee has been added successfully.");
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    getEmployee() {
      this.$store
        .dispatch("getEmployee")
        .then(
          function (result) {
            result.forEach((employee) => {
              this.received_by.options.push({
                id: employee.id,
                full_name:
                  employee?.get("fname") + " " + employee?.get("lname"),
                fname: employee?.get("fname"),
                mname: employee?.get("mname"),
                lname: employee?.get("lname"),
                office: employee?.get("office"),
              });
              this.received_from.options.push({
                id: employee.id,
                full_name:
                  employee?.get("fname") + " " + employee?.get("lname"),
                fname: employee?.get("fname"),
                mname: employee?.get("mname"),
                lname: employee?.get("lname"),
                office: employee?.get("office"),
              });
              this.assigned_person.options.push({
                id: employee.id,
                full_name:
                  employee?.get("fname") + " " + employee?.get("lname"),
                fname: employee?.get("fname"),
                mname: employee?.get("mname"),
                lname: employee?.get("lname"),
                office: employee?.get("office"),
              });
            });
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },
    // End Location API

    getStatus() {
      this.$store
        .dispatch("getStatus")
        .then(
          function (result) {
            result.forEach((status) => {
              this.status.options.push({
                id: status.id,
                name: status.get("name"),
              });
            });
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },

    getItem() {
      this.$store
        .dispatch("getItem")
        .then(
          function (result) {
            result.forEach((item) => {
              this.item.options.push(item.get("property_name"));
            });
          }.bind(this)
        )
        .catch((error) => {
          this.$toast.error(error);
        });
    },

    onSubmit() {
      this.$emit("form", this.form);
    },
    closeModal() {
      this.$emit("closeModal", false);
    },

    model(event, field) {
      this.form[field] = event;
    },
  },
  watch: {
    "form.purchaser": function (val) {
      if (val === "Regional Office") {
        this.form.serial_number = "ABCD - ";
      } else {
        this.form.serial_number = null;
      }
    },
  },
  mounted() {
    this.getCategory();
    this.getLocation();
    this.getEmployee();
    this.getStatus();
    this.getItem();
    if (this.edit_data) {
      console.log(this.edit_data);
      this.form = this.edit_data;
    }
  },
};
</script>

<style scoped></style>