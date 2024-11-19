<template>
  <v-container>
    <!-- Таблица -->
    <v-data-table
        :headers="headers"
        :items="filteredUsers"
        item-value="id"
        class="elevation-1"

    >
      <!-- Поля поиска над таблицей -->
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Телефонный справочник</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn @click="showAddForm = true" color="primary">Добавить</v-btn>

        </v-toolbar>
        <v-row >
          <v-col cols="3">
            <v-text-field
                v-model="search.firstName"
                label="Поиск по имени"
                clearable
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
                v-model="search.lastName"
                label="Поиск по фамилии"
                clearable
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
                v-model="search.address"
                label="Поиск по адресу"
                clearable
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
                v-model="search.phone"
                label="Поиск по телефону"
                clearable
            ></v-text-field>
          </v-col>
        </v-row>
      </template>

      <!-- Слот для столбца действий -->
      <template v-slot:[`item.actions`]="{ item }">
        <div class="d-flex justify-center">
          <v-btn icon @click="deleteUser(item.id)">
            <v-icon color="red">mdi-delete</v-icon>
          </v-btn>
        </div>
      </template>
    </v-data-table>

    <!-- Диалог добавления пользователя -->
    <v-dialog v-model="showAddForm" max-width="500px">
      <v-card>
        <v-card-title>Добавить пользователя</v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid">
            <v-text-field v-model="newUser.firstName" label="Имя" required />
            <v-text-field v-model="newUser.lastName" label="Фамилия" required />
            <v-text-field v-model="newUser.address" label="Адрес" required />
            <v-text-field v-model="newUser.phone" label="Телефон" required />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="blue darken-1" text @click="showAddForm = false">
            Отмена
          </v-btn>
          <v-btn :disabled="!valid" color="green darken-1" text @click="addUser">
            Добавить
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import apiClient from '../plugins/axios';

export default {
  data() {
    return {
      nazv:[
        {text: 'Имя'},
        {text: 'Имя'},
        {text: 'Имя'},
        {text: 'Имя'},
        {text: 'Имя'},
      ],
      headers: [
        { text: 'Имя', value: 'firstName' },
        { text: 'Фамилия', value: 'lastName' },
        { text: 'Адрес', value: 'address' },
        { text: 'Телефон', value: 'phone' },
        {
          text: 'Действия',
          value: 'actions',
          sortable: false,
          width: '50px', // Задаём минимальную ширину
        },
      ],
      users: [],
      search: {
        firstName: '',
        lastName: '',
        address: '',
        phone: '',
      },
      showAddForm: false,
      newUser: { firstName: '', lastName: '', address: '', phone: '' },
      valid: false,
    };
  },
  computed: {
    filteredUsers() {
      return this.users.filter((user) => {
        return (
            (!this.search.firstName ||
                user.firstName
                    .toLowerCase()
                    .includes(this.search.firstName.toLowerCase())) &&
            (!this.search.lastName ||
                user.lastName
                    .toLowerCase()
                    .includes(this.search.lastName.toLowerCase())) &&
            (!this.search.address ||
                user.address
                    .toLowerCase()
                    .includes(this.search.address.toLowerCase())) &&
            (!this.search.phone ||
                user.phone.toLowerCase().includes(this.search.phone.toLowerCase()))
        );
      });
    },
  },
  methods: {
    fetchUsers() {
      apiClient.get('/users').then((response) => {
        this.users = response.data;
      });
    },
    addUser() {
      apiClient.post('/users', this.newUser).then(() => {
        this.fetchUsers();
        this.showAddForm = false;
      });
    },
    deleteUser(id) {
      if (confirm('Вы уверены, что хотите удалить эту запись?')) {
        apiClient.delete(`/users/${id}`).then(() => {
          this.fetchUsers();
        });
      }
    },
  },
  mounted() {
    this.fetchUsers();
  },
};
</script>

<style scoped>
/* Минимальная ширина для столбца действий */
.v-data-table__wrapper th:nth-child(5),
.v-data-table__wrapper td:nth-child(5) {
  width: 50px;
  text-align: center;
}
</style>
