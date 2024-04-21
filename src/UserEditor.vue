<template>
  <v-card-text>
    <v-text-field v-model="editedUser.firstname" label="Имя"></v-text-field>
    <v-text-field v-model="editedUser.gender" label="Пол"></v-text-field>
    <v-text-field v-model="editedUser.email" label="Email"></v-text-field>
    <v-btn @click="saveChanges" :disabled="!isUserChanged">Сохранить</v-btn>
  </v-card-text>
</template>

<script>
  export default {
    name: "UserEditor",
    props: {
      initialUser: {
        type: Object,
        required: true,
      },
    },
    data() {
      return {
        editedUser: { ...this.initialUser },
        isUserChanged: false,
      };
    },
    watch: {
      editedUser: {
        deep: true,
        handler() {
          this.checkChanges();
        },
      },
      initialUser: {
        deep: true,
        handler(newValue) {
          this.editedUser = { ...newValue };
          this.checkChanges();
        },
      },
    },
    methods: {
      saveChanges() {
        this.$emit("updateUser", this.editedUser);
      },
      checkChanges() {
        this.isUserChanged =
          JSON.stringify(this.editedUser) !== JSON.stringify(this.initialUser);
      },
    },
  };
</script>
