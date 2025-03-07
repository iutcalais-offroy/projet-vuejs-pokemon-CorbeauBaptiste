<template>
  <n-card>
    <n-tabs
      class="card-tabs"
      default-value="signin"
      size="large"
      animated
      pane-wrapper-style="margin: 0 -4px"
      pane-style="padding-left: 4px; padding-right: 4px; box-sizing: border-box;"
    >
      <n-tab-pane name="signin" tab="Login">
        <n-form>
          <n-form-item-row label="Nom d'Utilisateur">
            <n-input />
          </n-form-item-row>
          <n-form-item-row label="Mot de Passe">
            <n-input />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" block secondary strong>
          Login
        </n-button>
      </n-tab-pane>
      <n-tab-pane name="signup" tab="Créer Compte">
        <n-form>
          <n-form-item-row label="Nom d'Utilisateur">
            <n-input />
          </n-form-item-row>
          <n-form-item-row label="Mot de Passe">
            <n-input />
          </n-form-item-row>
          <n-form-item-row label="Réentrer Mot de Passe">
            <n-input />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" block secondary strong>
          Créer Compte
        </n-button>
      </n-tab-pane>
    </n-tabs>
  </n-card>
</template>

<style scoped>
.card-tabs .n-tabs-nav--bar-type {
  padding-left: 4px;
}
</style>