<template>
  <v-main>
    <v-img contain height="100"></v-img>
    <v-alert
      v-if="this.error"
      dismissible
      border="right"
      color="red"
      type="success"
      >Wrong username / password!</v-alert
    >
    <v-card class="mx-auto" color="#A9A9A9" dark max-width="400" elevation="7">
      <v-card-title>
        <v-icon color="black">mdi-account</v-icon>
        <span class="title font-weight-bold text--primary"
          >Admin Login Here</span
        >
      </v-card-title>

      <v-card-text>
        <form @submit.prevent="login">
          <v-text-field
            v-model="loginData.email"
            label="Username"
            prepend-icon="mdi-account-circle"
            hint="Masukkan username anda"
          />
          <v-text-field
            v-model="loginData.password"
            label="Password"
            type="password"
            prepend-icon="mdi-lock"
          />
          <v-row justify="center">
            <v-btn type="submit">Login</v-btn>
          </v-row>
        </form>
      </v-card-text>
      <v-divider></v-divider>
    </v-card>
    <v-row align="center" justify="center">
      <!--<v-img
        contain
        lazy-src="../assets/logo.png"
      ></v-img> -->
    </v-row>
  </v-main>
</template>

<script>
export default {
  layout: "login",
  name: "IndexPage",
  data: () => ({
    loginData: {
      email: "",
      password: "",
    },
    error: false,
  }),
};
</script>
