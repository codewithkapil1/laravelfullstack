<template>
  <div class="container">
    <h3 align="center" class="mt-5">Employee Management</h3>
    <div class="row">
      <div class="col-md-2"></div>
      <div class="col-md-8">
        <div class="search-area glossy-background">
          <v-text-field
            v-model="search"
            label="Search Employee"
            append-icon="mdi-magnify"
            @input="searchEmployee"
          ></v-text-field>
        </div>
        <div class="form-area glossy-background">
          <form @submit.prevent="save" id="check-register-form">
            <div class="row">
              <div class="col-md-6">
                <v-text-field
                  v-model="employee.name"
                  label="Employee Name"
                  required
                ></v-text-field>
              </div>
              <div class="col-md-6">
                <v-text-field
                  v-model="employee.address"
                  label="Employee Address"
                  required
                ></v-text-field>
              </div>
            </div>
            <div class="row">
              <div class="col-md-12">
                <v-text-field
                  v-model="employee.phone"
                  label="Phone"
                  required
                ></v-text-field>
              </div>
            </div>
            <div class="row">
              <div class="col-md-12 mt-3">
                <v-btn type="submit" color="success" form="check-register-form"
                  >Save</v-btn
                >
              </div>
            </div>
          </form>
        </div>
        <div class="data-area glossy-background mt-5">
          <v-data-table
            :headers="headers"
            :items="paginatedData"
            :items-per-page="perPage"
            class="elevation-1"
          >
            <template v-slot:item.action="{ item }">
              <v-btn type="button" color="info" @click="edit(item)">Edit</v-btn>

              <v-btn type="button" color="danger" @click="remove(item)"
                >Delete</v-btn
              >
            </template>
          </v-data-table>
          <v-pagination
            v-model="page"
            :length="pageCount"
            @input="changePage"
          ></v-pagination>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "redaxios";
import { ref } from "vue";
import {
  VTextField,
  VBtn,
  VTable,
  VDataTable,
  VPagination,
  VIcon,
} from "vuetify/components";

export default {
  name: "Employee",
  components: {
    VTextField,
    VBtn,
    VTable,
    VDataTable,
    VPagination,
    VIcon,
  },
  data() {
    return {
      result: [],
      employee: {
        id: "",
        name: "",
        address: "",
        phone: "",
      },
      search: "",
      page: 1,
      perPage: 5,
      headers: [
        { text: "Employee ID", value: "id" },
        { text: "Employee Name", value: "name" },
        { text: "Address", value: "address" },
        { text: "Phone", value: "phone" },
        { text: "Action", value: "action", sortable: false },
      ],
    };
  },
  computed: {
    pageCount() {
      return Math.ceil(this.filteredData.length / this.perPage);
    },
    filteredData() {
      if (this.search) {
        return this.result.filter((employee) =>
          employee.name.toLowerCase().includes(this.search.toLowerCase())
        );
      }
      return this.result;
    },
    paginatedData() {
      const start = (this.page - 1) * this.perPage;
      const end = start + this.perPage;
      return this.filteredData.slice(start, end);
    },
  },
  created() {
    this.EmployeeLoad();
  },
  methods: {
    EmployeeLoad() {
      var page = "http://127.0.0.1:8000/api/employee";
      axios.get(page).then(({ data }) => {
        this.result = data;
      });
    },
    save() {
      if (this.employee.id == "") {
        this.saveData();
      } else {
        this.updateData();
      }
    },
    saveData() {
      axios
        .post("http://127.0.0.1:8000/api/employee", this.employee)
        .then(() => {
          this.EmployeeLoad();
          this.employee = { id: "", name: "", address: "", phone: "" };
        });
    },
    edit(employee) {
      this.employee = { ...employee };
    },
    updateData() {
      var editrecords =
        "http://127.0.0.1:8000/api/employee/" + this.employee.id;
      axios.put(editrecords, this.employee).then(() => {
        this.employee = { id: "", name: "", address: "", phone: "" };
        this.EmployeeLoad();
      });
    },
    remove(employee) {
      var url = `http://127.0.0.1:8000/api/employee/${employee.id}`;
      axios.delete(url).then(() => {
        this.EmployeeLoad();
      });
    },
    searchEmployee() {
      this.page = 1; // Reset to first page after search
    },
    changePage(newPage) {
      this.page = newPage;
    },
  },
};
</script>

<style scoped>
.search-area {
  padding: 10px 20px;
  margin-bottom: 20px;
  background: rgba(255, 255, 255, 0.6);
  color: #000;
  border-radius: 15px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.form-area,
.data-area {
  padding: 20px;
  margin-top: 20px;
  background: rgba(255, 255, 255, 0.6);
  color: #000;
  border-radius: 15px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.glossy-background {
  transition: transform 0.3s, box-shadow 0.3s;
}

.v-btn {
  background-color: red;
  margin-right: 2rem;
}
/* .v-btn:hover {
  background-color: red;
  color: #fff;
} */
</style>
