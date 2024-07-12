<template>
  <div v-if="client">
    <!-- Titre et bouton supprimer -->
    <div class="row border-bottom pb-3 mb-3">
      <div class="col">
        <h1 v-if="isNewClient" class="h3">
          <i class="fa-solid fa-angle-down me-2" />Ajouter un client
        </h1>
        <h1 v-else class="h3">
          <i class="fa-solid fa-angle-down me-2" />Editer un client
        </h1>
      </div>
      <div v-if="!isNewClient" class="col text-end">
        <button @click="onDeleteClient(client)" class="btn btn-outline-danger">
          <i class="fa-solid fa-trash me-2" />
          Supprimer un client
        </button>
      </div>
    </div>

    <div class="row">
      <div class="col-md-5">
        <h2 class="h5">Contact</h2>
        <div class="form-floating mb-3">
          <input type="text" name="firstName" id="firstName" v-model="client.firstName" class="form-control"
            placeholder="Prénom" :class="{ 'is-invalid': !client.firstName }" />
          <label for="firstName" class="form-label">Prénom</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="lastName" id="lastName" v-model="client.lastName" class="form-control"
            placeholder="Nom" :class="{ 'is-invalid': !client.lastName }" />
          <label for="lastName" class="form-label">Nom</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="fonction" id="fonction" v-model="client.fonction" class="form-control"
            placeholder="Fonction" :class="{ 'is-invalid': !client.fonction }" />
          <label for="fonction" class="form-label">Fonction</label>
        </div>
        <div class="form-floating mb-3">
          <input type="tel" name="phone" id="phone" v-model="client.phone" class="form-control" placeholder="Téléphone"
            :class="{ 'is-invalid': !client.phone }" />
          <label for="phone" class="form-label">Téléphone</label>
        </div>
        <div class="form-floating mb-3">
          <input type="email" name="email" id="email" v-model="client.email" class="form-control" placeholder="Email"
            :class="{ 'is-invalid': !client.email }" />
          <label for="email" class="form-label">Email</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="companyName" id="companyName" v-model="client.companyName" class="form-control"
            placeholder="Entreprise" :class="{ 'is-invalid': !client.companyName }" />
          <label for="companyName" class="form-label">Entreprise</label>
        </div>
      </div>

      <div class="col-md-5">
        <h2 class="h5">Coordonnées</h2>
        <div class="form-floating mb-3">
          <input type="text" name="address1" id="address1" v-model="client.address1" class="form-control"
            placeholder="Adresse 1" :class="{ 'is-invalid': !client.address1 }" />
          <label for="address1" class="form-label">Adresse 1</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="address2" id="address2" v-model="client.address2" class="form-control"
            placeholder="Adresse 2" />
          <label for="address2" class="form-label">Adresse 2</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="zip" id="zip" v-model="client.zip" class="form-control" placeholder="Code Postal"
            :class="{ 'is-invalid': !client.zip }" />
          <label for="zip" class="form-label">Code Postal</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="city" id="city" v-model="client.city" class="form-control" placeholder="Ville"
            :class="{ 'is-invalid': !client.city }" />
          <label for="city" class="form-label">Ville</label>
        </div>
        <div class="form-floating mb-3">
          <input type="text" name="country" id="country" v-model="client.country" class="form-control"
            placeholder="Pays" :class="{ 'is-invalid': !client.country }" />
          <label for="country" class="form-label">Pays</label>
        </div>
      </div>
      <div class="col-md-2">
        <h2 class="h5">Date d'ajout</h2>
        <div class="form-floating mb-3">
          <input type="date" name="date" id="date" v-model="currentDate" class="form-control">
          <label for="date" class="form-label">Date</label>
        </div>
      </div>
    </div>

    <p class="text-end">
      <button @click="onSave()" :disabled="formInvalid" class="btn btn-outline-primary btn-lg px-5">
        <i class="fa-solid fa-save me-2" />Enregistrer
      </button>
    </p>
    <AppDebug :datas="client" />
  </div>
</template>

<script>
import AppDebug from '@/components/AppDebug.vue';
import ClientTableList from '@/components/tables/ClientTableList.vue';
import { clientInterface } from '@/interfaces/client';
import { useClientStore } from '@/stores/client.js'
import { mapActions, mapState, mapWritableState } from 'pinia'

export default {
  components: {
    ClientTableList
  },
  props: {
    id: {
      type: String,
      default: 'new'
    }
  },
  data() {
    return {
      currentDate: new Date().toISOString().substr(0, 10),
    }
  },
  async mounted() {
    await this.setClient(this.id);
    if (this.client && this.id !== 'new' && this.client.date) {
      this.currentDate = this.client.date; // Utilisez la date du client si disponible
    } else {
      this.currentDate = this.formatDate(new Date()); // Sinon, utilisez la date actuelle
    }
  },
  computed: {
    ...mapState(useClientStore, {
      loading: 'loading'
    }),
    ...mapWritableState(useClientStore, { client: 'item' }),

    isNewClient() {
      return this.id == 'new'
    },

    formInvalid() {
      return (
        !this.client ||
        !this.client.firstName ||
        !this.client.lastName ||
        !this.client.fonction ||
        !this.client.phone ||
        !this.client.email ||
        !this.client.companyName ||
        !this.client.address1 ||
        !this.client.zip ||
        !this.client.city ||
        !this.client.country
      )
    }
  },
  methods: {
    ...mapActions(useClientStore, {
      deleteClient: 'deleteItem',
      updateClient: 'updateItem',
      createClient: 'createItem',
      setClient: 'setItem'
    }),
    formatDate(date) {
      let month = '' + (date.getMonth() + 1),
        day = '' + date.getDate(),
        year = date.getFullYear();

      if (month.length < 2)
        month = '0' + month;
      if (day.length < 2)
        day = '0' + day;

      return [year, month, day].join('-'); // Ajustement pour le format yyyy-MM-dd
    },
    formatDateToDDMMYYYY(date) {
      const d = new Date(date),
        day = '' + d.getDate(),
        month = '' + (d.getMonth() + 1),
        year = d.getFullYear();

      return [day.padStart(2, '0'), month.padStart(2, '0'), year].join('-');
    },

    onSave() {
      if (this.formInvalid) {
        // gestion des erreurs ici
        this.error = true;
      } else {
        this.error = false;
        // Utilisez formatDateToDDMMYYYY pour formater la date
        this.client.date = this.formatDateToDDMMYYYY(this.currentDate);
        if (this.isNewClient) {
          this.createClient(this.client);
        } else {
          this.updateClient(this.client);
        }

        this.$router.push({ path: '/clients' });
      }
    },

    onDeleteClient(client) {
      this.deleteClient(client)
      this.$router.push({ path: '/clients' })
    }
  }
}
</script>

<style scoped>
.form-floating {
  margin-bottom: 1rem;
}
</style>
