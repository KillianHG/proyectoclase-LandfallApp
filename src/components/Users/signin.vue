<template>
  <div class="form">
    <div class="inner-content">
      <h1>Inicia sesión</h1>
      <v-layout row v-if="error">
        <v-flex xs12 sm6 offset-sm3>
          <app-alert @dismissed="onDismissed" :text="error.message"></app-alert>
        </v-flex>
      </v-layout>
      <form>
        <v-text-field
          v-model="email"
          :error-messages="emailErrors"
          label="E-mail"
          required
          @input="$v.email.$touch()"
          @blur="$v.email.$touch()"
        ></v-text-field>
        <v-text-field
          type="password"
          v-model="password"
          :error-messages="passwordErrors"
          label="Password"
          required
          @input="$v.password.$touch()"
          @blur="$v.password.$touch()"
        ></v-text-field>
        <v-btn @click="onSignin">Enter</v-btn>
        <v-btn @click="clear">Clear</v-btn>
      </form>
    </div>
  </div>
</template>
<script>
  import { validationMixin } from 'vuelidate'
  import { required, email, minLength } from 'vuelidate/lib/validators'

  export default {
    name: 'signup',
    mixins: [validationMixin],

    validations: {
      email: { required, email },
      password: { required, minLength: minLength(6) }
    },

    data: () => ({
      email: '',
      password: ''
    }),

    computed: {
      emailErrors () {
        const errors = []
        if (!this.$v.email.$dirty) return errors
        !this.$v.email.email && errors.push('Must be valid e-mail')
        !this.$v.email.required && errors.push('E-mail is required')
        return errors
      },
      passwordErrors () {
        const errors = []
        if (!this.$v.password.$dirty) return errors
        !this.$v.password.minLength && errors.push('Must be minimum 6 characters password')
        !this.$v.password.required && errors.push('Password is required')
        return errors
      },
      user () {
        return this.$store.getters.user
      },
      error () {
        return this.$store.getters.error
      },
      loading () {
        return this.$store.getters.loading
      }
    },
    watch: {
      user (value) {
        if (value !== null && value !== undefined) {
          this.$router.push('/')
        }
      }
    },
    methods: {
      clear () {
        this.$v.$reset()
        this.email = ''
        this.password = ''
      },
      onSignin () {
        this.$store.dispatch('signUserIn', {email: this.email, password: this.password})
      },
      onDismissed () {
        this.$store.dispatch('clearError')
      }
    }
  }
</script>

<style lang="scss">

  .form{
    display: flex;
    justify-content: center;
    align-items: center;
    //margin: 10vw 0;

    .inner-content {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      width: 80%;
      background-color: rgba(1, 1, 1, 0.75);
      border-radius: 12px;
      padding: 10px;

      h1 {
        border-bottom: 1px solid white;
        width: 80%;
        text-align: center;
        margin-bottom: 25px;
      }

      form {
        width: 80%;
      }
    }
  }

</style>

