<template>
  <div>
    <div class="login">
      <a class="btn facebook" href="/login/facebook"> LOGIN WITH FACEBOOK </a>
      <a class="btn google" href="/login/google"> LOGIN WITH GOOGLE </a>
    </div>
    <v-form v-model="valid" ref="form" lazy-validation>
      <v-text-field label="Email" v-model="email" :rules="emailRules" id="email" required></v-text-field>
      <v-text-field label="Password" v-model="password" id="password" required></v-text-field>
      <v-btn @click="submit" :disabled="!valid" id="login">
        submit
      </v-btn>
      <v-btn @click="clear" id="clear_input">clear</v-btn>
    </v-form>
  </div>
</template>

<script>
import axios from 'axios';
import bus from './../bus';

export default {
  data: () => ({
    valid: true,
    email: '',
    password: '',
    emailRules: [
      v => !!v || 'E-mail is required',
      v => /\S+@\S+\.\S+/.test(v) || 'E-mail must be valid',
    ],
  }),
  methods: {
    async submit() {
      return axios({
          method: 'post',
          data: {
            email: this.email,
            password: this.password,
          },
          url: 'http://localhost:8081/users/login',
          headers: {
            'Content-Type': 'application/json',
          },
        })
        .then((response) => {
          window.localStorage.setItem('auth', response.data.token);
          this.$swal('Great!', 'You are ready to start!', 'success');
          bus.$emit('refreshUser');
          this.$router.push({
            name: 'Home'
          });
        })
        .catch((error) => {
          const messages = error.response.data.messages;
          this.$swal('Oh oo!', `${messages}`, 'error');
          this.$router.push({
            name: 'Login'
          });
        });
    },
    clear() {
      this.$refs.form.reset();
    },
  },
};
</script>