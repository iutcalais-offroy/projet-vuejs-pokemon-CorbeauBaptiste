<template>
  <n-card>
    <n-tabs
      class="card-tabs"
      v-model:value="activeTab"
      size="large"
      animated
      pane-wrapper-style="margin: 0 -4px"
      pane-style="padding-left: 4px; padding-right: 4px; box-sizing: border-box;"
    >
      <n-tab-pane name="signin" tab="Login">
        <n-form>
          <n-form-item-row label="Nom d'Utilisateur">
            <n-input v-model="loginData.username" />
          </n-form-item-row>
          <n-form-item-row label="Mot de Passe">
            <n-input type="password" v-model="loginData.password" />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" block secondary strong @click="handleLogin">
          Login
        </n-button>
      </n-tab-pane>
      <n-tab-pane name="signup" tab="Créer Compte">
        <n-form>
          <n-form-item-row label="Nom d'Utilisateur">
            <n-input v-model="signupData.username" />
          </n-form-item-row>
          <n-form-item-row label="Mot de Passe">
            <n-input type="password" v-model="signupData.password" />
          </n-form-item-row>
          <n-form-item-row label="Réentrer Mot de Passe">
            <n-input type="password" v-model="signupData.confirmPassword" />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" block secondary strong @click="handleSignup">
          Créer Compte
        </n-button>
      </n-tab-pane>
    </n-tabs>
  </n-card>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const activeTab = ref("signin"); // Définit l'onglet actif par défaut sur Login
const loginData = ref({ username: '', password: '' });
const signupData = ref({ username: '', password: '', confirmPassword: '' });

const handleSignup = () => {
  if (signupData.value.password !== signupData.value.confirmPassword) {
    alert("Les mots de passe ne correspondent pas");
    return;
  }

  let users = JSON.parse(localStorage.getItem("users")) || [];

  if (!Array.isArray(users)) {
    users = [];
  }

  const newUser = {
    id: Date.now(),
    username: signupData.value.username,
    password: signupData.value.password,
  };

  users.push(newUser);
  localStorage.setItem("users", JSON.stringify(users));

  alert("Compte créé avec succès! Connectez-vous maintenant");
  activeTab.value = "signin"; // Basculer automatiquement vers l'onglet Login
};

const handleLogin = () => {
  const users = JSON.parse(localStorage.getItem('users') || '[]');
  const user = users.find(user => user.username === loginData.value.username && user.password === loginData.value.password);
  
  if (!user) {
    alert("Identifiants incorrects");
    return;
  }

  localStorage.setItem('token', JSON.stringify({ userId: user.id, token: 'token' }));
  router.push({ path: '/deck-builder' });
};
</script>

<style scoped>
.card-tabs .n-tabs-nav--bar-type {
  padding-left: 4px;
}
</style>