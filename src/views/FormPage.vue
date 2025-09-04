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

// Validation Rules
const rules = {
  required: (v) => !!v || "This field is required",
  email: (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
};

// Snackbar
const snackbar = ref(false);
const snackbarMessage = ref("");

// Submit Function
const submitForm = async () => {
  if (!form.value.validate()) return;

  try {
    await addDoc(collection(db, "interns"), {
      name: name.value,
      email: email.value,
      role: role.value,
      dateJoined: dateJoined.value,
    });

    snackbarMessage.value = "Intern added successfully!";
    snackbar.value = true;

    // Clear form
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
    <v-card class="pa-6" elevation="4">
      <v-card-title>Intern Registration Form</v-card-title>

      <v-form ref="form" v-model="valid" @submit.prevent="submitForm">
        <!-- Full Name -->
        <v-text-field
            v-model="name"
            label="Full Name"
            :rules="[rules.required]"
            required
        />

        <!-- Email -->
        <v-text-field
            v-model="email"
            label="Email"
            :rules="[rules.required, rules.email]"
            required
        />

        <!-- Role -->
        <v-select
            v-model="role"
            :items="roles"
            label="Role"
            :rules="[rules.required]"
            required
        />

        <!-- Date Joined -->
        <v-text-field
            v-model="dateJoined"
            label="Date Joined"
            type="date"
        />

        <!-- Submit -->
        <v-btn type="submit" color="primary" class="mt-4">Submit</v-btn>
      </v-form>

      <!-- Snackbar -->
      <v-snackbar v-model="snackbar" color="green" timeout="3000">
        {{ snackbarMessage }}
      </v-snackbar>
    </v-card>
  </v-container>
</template>


