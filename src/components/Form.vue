<template>
  <div>
    <v-card class="pa-4 ma-2">
      <v-card-title class="d-flex justify-center">File a Complaint</v-card-title>
      <v-form>
        <v-card-text>
          <v-text-field
            v-model.lazy="userData.email"
            :error-messages="emailErrors"
            label="E-mail"
            required
            filled
            shaped
            @input="$v.userData.$touch()"
            @blur="$v.userData.$touch()"
          ></v-text-field>
          <v-text-field
            v-model.lazy="userData.passworld"
            :error-messages="passworldErrors"
            :counter="10"
            label="Passworld"
            required
            filled
            shaped
            :type="show ? 'text' : 'password'"
            :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
            @click:append="show = !show"
            @input="$v.userData.passworld.$touch()"
            @blur="$v.userData.passworld.$touch()"
          ></v-text-field>
          <v-text-field
            v-model.lazy="userData.age"
            :error-messages="ageErrors"
            label="Age"
            required
            filled
            shaped
            @input="$v.userData.age.$touch()"
            @blur="$v.userData.age.$touch()"
          ></v-text-field>
          <v-textarea
            v-model.lazy="userData.message"
            label="Message"
            shaped
            rows="5"
            auto-grow
            outlined
            row-height="15"
          ></v-textarea>

          <v-card-actions>
            <v-btn class="mr-4" @click="submit">submit</v-btn>
            <v-btn @click="clear">clear</v-btn>
          </v-card-actions>
        </v-card-text>
      </v-form>
    </v-card>
    <hr />
    <v-card class="pa-4 ma-2">
      <v-card-title class="d-flex justify-center">Your Data</v-card-title>
      <v-card-text>
        <p>Mail: {{userData.email}}</p>
        <p>Passworld: {{userData.passworld}}</p>
        <p>Age: {{userData.age}}</p>
        <p>Message: {{userData.message}}</p>
        <p>Send Email?</p>
        <p>Gender:</p>
        <p>Priority:</p>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, maxLength, email, numeric } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],

  validations: {
    userData: {
      email: { required, email },
      passworld: { required, maxLength: maxLength(10) },
      age: { required, numeric }
    }
  },

  data: () => ({
    userData: {
      email: "",
      passworld: "",
      age: null,
      message: ""
    },
    show: false
  }),

  computed: {
    ageErrors() {
      const errors = [];
      if (!this.$v.userData.age.$dirty) return errors;
      !this.$v.userData.age.numeric && errors.push("age must be a number");
      !this.$v.userData.age.required && errors.push("age is required.");
      return errors;
    },
    passworldErrors() {
      const errors = [];
      if (!this.$v.userData.passworld.$dirty) return errors;
      !this.$v.userData.passworld.maxLength &&
        errors.push("passworld must be at most 10 characters long");
      !this.$v.userData.passworld.required &&
        errors.push("Passworld is required.");
      return errors;
    },
    emailErrors() {
      const errors = [];
      if (!this.$v.userData.email.$dirty) return errors;
      !this.$v.userData.email.email && errors.push("Must be valid e-mail");
      !this.$v.userData.email.required && errors.push("E-mail is required");
      return errors;
    }
  },

  methods: {
    submit() {
      this.$v.$touch();
    },
    clear() {
      this.$v.$reset();
      this.userData = [];
    }
  }
};
</script>