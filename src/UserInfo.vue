<template>
    <v-card class="mx-auto my-8" elevation="16" max-width="344">
      <v-card-title>{{ user.firstname }} {{ user.lastname }}</v-card-title>
      <v-card-text>Возраст: {{ calculateAge(user.birthday) }}</v-card-text>
      <v-card-text>Дата рождения: {{ user.birthday }}</v-card-text>
      <v-card-text>Пол: {{ user.gender }}</v-card-text>
      <v-card-text>Email: {{ user.email }}</v-card-text>
      <v-card-actions>
        <v-btn
          @click="isShowDialog = true"
          color="primary"
          text
        >Редактировать</v-btn>
      </v-card-actions>
      <v-dialog v-model="isShowDialog" max-width="500">
        <v-card>
          <v-card-title>Редактирование пользователя</v-card-title>
          <v-card-text>
            <user-editor :initialUser="user" @updateUser="updateUserHandler" />
          </v-card-text>
        </v-card>
      </v-dialog>
    </v-card>
  </template>
  
  <script>
  import UserEditor from './UserEditor.vue';
  
  export default {
    name: "UserInfo",
    props: {
      user: {
        type: Object,
        required: true,
      },
    },
    data() {
      return {
        isShowDialog: false,
        editedUser: null,
      };
    },
    methods: {
      calculateAge(birthday) {
        const today = new Date();
        const birthDate = new Date(birthday);
        let age = today.getFullYear() - birthDate.getFullYear();
        const monthDiff = today.getMonth() - birthDate.getMonth();
        if (
          monthDiff < 0 ||
          (monthDiff === 0 && today.getDate() < birthDate.getDate())
        ) {
          age--;
        }
        return age;
      },
      updateUserHandler(updatedUser) {
      this.$emit('updateUser', updatedUser); 
      this.isShowDialog = false;
    },
    },
    components: {
      UserEditor,
    },
  };
  </script>