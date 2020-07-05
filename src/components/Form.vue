<template>
  <div>
    <v-card class="pa-4 ma-2">
      <v-card-title class="d-flex justify-center">File a Complaint</v-card-title>
      <v-form>
        <v-card-text>
          <v-row>
            <!-- e-mail -->
            <v-col xl="5" md="5" lg="5" sm="6" cols="12">
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
            </v-col>
            <!-- password -->
            <v-col xl="5" md="5" lg="5" sm="6" cols="12">
              <v-text-field
                v-model.lazy="userData.passworld"
                :error-messages="passworldErrors"
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
            </v-col>
            <!-- age -->
            <v-col xl="2" md="2" lg="2" sm="3" cols="6">
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
            </v-col>
            <!-- switch -->
            <v-col xl="5" md="5" lg="5" sm="3" cols="6">
              <app-switch v-model="dataSwitch"></app-switch>
            </v-col>
            <!-- sent email ? -->
            <v-col xl="5" md="5" lg="5" sm="3" cols="6">
              <v-checkbox class="ma-0" v-model="sendMail" label="E-mail" value="SendMail"></v-checkbox>
              <v-checkbox class="ma-0" v-model="sendMail" label="Infomail" value="SendInfomail"></v-checkbox>
            </v-col>
            <!-- gender -->
            <v-col xl="2" md="2" lg="2" sm="3" cols="6">
              <v-radio-group
                class="ma-0"
                v-model="userData.gender"
                :error-messages="genderErrors"
                column
                required
                @input="$v.userData.gender.$touch()"
                @blur="$v.userData.gender.$touch()"
              >
                <v-radio label="Male" value="male"></v-radio>
                <v-radio class="my-3" label="Female" value="female"></v-radio>
              </v-radio-group>
            </v-col>
            <!-- priority -->
            <v-col cols="12">
              <v-select
                v-model="selectedPriority"
                :error-messages="priorityErrors"
                label="Priority"
                :items="itemsPriority"
                required
                @input="$v.selectedPriority.$touch()"
                @blur="$v.selectedPriority.$touch()"
              ></v-select>
            </v-col>
            <!-- message -->
            <v-col cols="12">
              <v-textarea
                v-model.lazy="message"
                label="Message"
                shaped
                rows="5"
                auto-grow
                outlined
                row-height="15"
              ></v-textarea>
            </v-col>
          </v-row>
          <v-card-actions>
            <v-btn class="mr-4" @click.prevent="submit">submit</v-btn>
            <v-btn @click="clear">clear</v-btn>
          </v-card-actions>
        </v-card-text>
      </v-form>
    </v-card>
    <hr />
    <v-card class="pa-4 ma-2" v-if="isSubmited">
      <v-card-title class="d-flex justify-center">Your Data</v-card-title>
      <v-card-text>
        <v-simple-table>
          <template v-slot:default>
            <tbody>
              <tr>
                <td>E-mail</td>
                <td>{{ userData.email}}</td>
              </tr>
              <tr>
                <td>Passworld</td>
                <td>{{ userData.passworld }}</td>
              </tr>
              <tr>
                <td>Age</td>
                <td>{{ userData.age }}</td>
              </tr>
              <tr>
                <td>Gender</td>
                <td>{{ userData.gender }}</td>
              </tr>
              <tr>
                <td>Priority</td>
                <td>{{ selectedPriority }}</td>
              </tr>
              <tr>
                <td>Switch</td>
                <td>{{ dataSwitch}}</td>
              </tr>
              <tr>
                <td>Message</td>
                <td>{{ message }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, minLength, email, numeric } from "vuelidate/lib/validators";
import Switch from "./Switch";

export default {
  components: { appSwitch: Switch },
  mixins: [validationMixin],
  validations: {
    userData: {
      email: { required, email },
      passworld: { required, minLength: minLength(10) },
      age: { required, numeric },
      gender: { required }
    },
    selectedPriority: { required }
  },
  data: () => ({
    userData: {
      email: "",
      passworld: "",
      age: null,
      gender: ""
    },
    message: "",
    sendMail: [],
    itemsPriority: ["High", "Medium", "Low"],
    selectedPriority: null,
    show: false,
    dataSwitch: true,
    isSubmited: false
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
      !this.$v.userData.passworld.minLength &&
        errors.push("passworld must be at least 10 characters");
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
    },
    genderErrors() {
      const errors = [];
      if (!this.$v.userData.gender.$dirty) return errors;
      !this.$v.userData.gender.required && errors.push("Gender is required");
      return errors;
    },
    priorityErrors() {
      const errors = [];
      if (!this.$v.selectedPriority.$dirty) return errors;
      !this.$v.selectedPriority.required && errors.push("Priority is required");
      return errors;
    }
  },

  methods: {
    submit() {
      this.$v.$touch();
      if (this.$v.$invalid) {
        console.log("ERROR!");
        this.submitStatus = "ERROR";
      } else {
        console.log("OK!");
        this.submitStatus = "OK";
        this.isSubmited = true;
      }
    },
    clear() {
      this.$v.$reset();
      this.userData = [];
      this.message = "";
      this.sendMail = [];
      this.selectedPriority = "";
      this.isSubmited = false;
    }
  }
};
</script>