<template>
  <v-app>
    <v-progress-linear color="primary" v-if="isLoading" absolute></v-progress-linear>
    <v-main class="container">
      <v-card class="pa-3">
        <v-autocomplete
          v-model="selectedUser"
          :items="users"
          item-text="firstname"
          item-value="id"
          label="Выберите пользователя"
          dense
          return-object
        ></v-autocomplete>
      </v-card>
      <user-info
        :user="selectedUser"
        v-if="selectedUser"
        @updateUser="updateUser"
      />
    </v-main>
    <v-snackbar v-model="isError" :timeout="5000" color="error">
      {{ errorMessage }}
      <template v-slot:action="{ attrs }">
        <v-btn color="white" text v-bind="attrs" @click="isError = false">Закрыть</v-btn>
      </template>
    </v-snackbar>
  </v-app>
</template>

<script>
import UserInfo from "./UserInfo.vue";

export default {
  name: "App",

  components: {
    UserInfo,
  },

  data: () => ({
    selectedUser: null,
    users: [],
    isLoading: false,
    isError: false,
    errorMessage: "",
  }),

  created() {
    this.getUsers();
  },

  methods: {
    async getUsers() {
      try {
        this.isLoading = true;
        const response = await fetch(
          "https://fakerapi.it/api/v1/persons?_locale=ru_RU"
        );
        const data = await response.json();
        this.users = data.data;
      } catch (error) {
        this.showError("Ошибка при получении списка пользователей:", error);
      } finally {
        this.isLoading = false;
      }
    },
    async updateUser(updatedUser) {
      this.isLoading = true;
      try {
        // если бы API fakerapi имело возможность принимать POST запросы на редактирование пользователя
        const response = await fetch(
          `https://fakerapi.it/api/v1/persons/${updatedUser.id}`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(updatedUser),
          }
        );
        if (response.ok) {
          this.selectedUser = updatedUser;
          this.showDialog = false;
        } else {
          this.showError(
            "Ошибка при обновлении пользователя:",
            response.statusText
          );
        }
      } catch (error) {
        this.showError("Ошибка при отправке запроса на сервер:", error);
      } finally {
        this.isLoading = false;
      }
    },
    showError(message, error) {
      this.isError = true;
      this.errorMessage = `${message} ${error}`;
    },
  },
};
</script>

<style>
.container {
  width: 1200px;
  margin: 30px auto;
}
</style>
