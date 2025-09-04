<script setup>
import { ref } from "vue";
import { db } from "../services/firebase.js";
import { collection, addDoc } from "firebase/firestore";

const valid = ref(false);
const form = ref(null);
const name = ref("");
const email = ref("");
const role = ref("");
const dateJoined = ref("");

const roles = ["Developer", "Designer", "Tester"];


const rules = {
  required: (v) => !!v || "This field is required",
  email: (v) => /.+@.+\..+/.test(v) || "e-mail must be valid",
};


const snackbar = ref(false);
const snackbarMessage = ref("");


const submitForm = async () => {

  const { valid } = await form.value.validate();
  if (!valid) return;

  try {
    await addDoc(collection(db, "interns"), {
      name: name.value,
      email: email.value,
      role: role.value,
      dateJoined: dateJoined.value,
    });

    snackbarMessage.value = "Intern added successfully!";
    snackbar.value = true;

    form.value.reset();


    name.value = "";
    email.value = "";
    role.value = "";
    dateJoined.value = "";
  } catch (error) {
    snackbarMessage.value = "Error: " + error.message;
    snackbar.value = true;
  }
};
</script>

<template>

  <v-container>
    <v-card class="pa-10" elevation="5">
      <v-card-title class="custom-title">Intern Registration Form</v-card-title>

      <v-form ref="form" v-model="valid" @submit.prevent="submitForm">


        <v-text-field
            v-model="name"
            label="Full Name"
            :rules="[rules.required]"
            required
        />

        <v-text-field
            v-model="email"
            label="Email"
            :rules="[rules.required, rules.email]"
            required
        />

        <v-select
            v-model="role"
            :items="roles"
            label="Role"
            :rules="[rules.required]"
            required
        />

        <v-text-field
            v-model="dateJoined"
            label="Date Joined"
            type="date"
        />


        <div class="d-flex justify-end mt-6">
          <v-btn type="submit" color="primary" class="btn">Submit</v-btn>
        </div>


      </v-form>

      <v-snackbar v-model="snackbar" color="green">
        {{ snackbarMessage }}
      </v-snackbar>
    </v-card>
  </v-container>
</template>



<style scoped>
.custom-title {
  background-color: #BBDEFB;
  color: #040505;
  font-weight: bold;
}
.btn{
  padding: 12px 150px;
  font-size: 16px;
  bottom: 0;
  margin: 10px;
  margin-left: 300px;
}

</style>

