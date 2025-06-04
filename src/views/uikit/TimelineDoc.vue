<template>
  <div class="card p-6">
    <h1 class="text-2xl font-bold mb-4">Liste des Parcelles</h1>

    <DataTable :value="parcelles" :paginator="true" :rows="10"
               :rowsPerPageOptions="[5, 10, 20, 50]" responsiveLayout="scroll"
               stripedRows class="p-datatable-gridlines">

      <Column field="proprietaire.nom" header="Propriétaire" :sortable="true">
        <template #body="slotProps">
          {{ slotProps.data.proprietaire.prenom }} {{ slotProps.data.proprietaire.nom }}
        </template>
      </Column>

      <Column header="Contact">
        <template #body="slotProps">
          📞 {{ slotProps.data.proprietaire.telephone }}<br />
          ✉️ {{ slotProps.data.proprietaire.email }}
        </template>
      </Column>

      <Column header="Adresse">
        <template #body="slotProps">
          {{ slotProps.data.adresse.avenue }}, {{ slotProps.data.adresse.quartier }},
          {{ slotProps.data.adresse.commune }}, {{ slotProps.data.adresse.ville }}
        </template>
      </Column>

      <Column field="taxes[0].type" header="Type Taxe" :sortable="true"></Column>

      <Column field="taxes[0].montant" header="Montant" :sortable="true">
        <template #body="slotProps">
          {{ formatCurrency(slotProps.data.taxes[0].montant) }}
        </template>
      </Column>

      <Column field="taxes[0].periode" header="Période" :sortable="true"></Column>

      <Column field="taxes[0].status" header="Statut" :sortable="true">
        <template #body="slotProps">
          <Tag :value="slotProps.data.taxes[0].status" :severity="getStatusSeverity(slotProps.data.taxes[0].status)"></Tag>
        </template>
      </Column>

    </DataTable>
  </div>
</template>

<script>
import axios from 'axios';
import Column from 'primevue/column';
import DataTable from 'primevue/datatable';
import Tag from 'primevue/tag'; // Import the Tag component for status display

export default {
  name: "ParcellesTable",
  components: {
    DataTable,
    Column,
    Tag
  },
  data() {
    return {
      parcelles: [],
    };
  },
  mounted() {
    this.fetchParcelles();
  },
  methods: {
    fetchParcelles() {
      axios
        .get("https://plankton-app-fjjah.ondigitalocean.app/parcelles/parcelles_details")
        .then((response) => {
          this.parcelles = response.data;
        })
        .catch((error) => {
          console.error("Erreur lors du chargement des données :", error);
          // Fallback data for demonstration if API fails
          this.parcelles = [
            {
              "id": "P-001",
              "proprietaire": { "nom": "Kalala", "prenom": "Christophe", "telephone": "0812345678", "email": "christophe@example.com" },
              "adresse": { "avenue": "Home", "quartier": "Jolie Park", "commune": "Ngaliema", "ville": "Kinshasa" },
              "dateEnregistrement": "2025-06-01T10:00:00.000Z",
              "taxes": [ { "type": "foncière", "montant": 500, "periode": "2025", "status": "En attente", "datePaiement": null } ]
            },
            {
              "id": "P-002",
              "proprietaire": { "nom": "Nsimba", "prenom": "Isabella", "telephone": "0823456789", "email": "isabella@example.com" },
              "adresse": { "avenue": "Mboma", "quartier": "LDK", "commune": "N'Sele", "ville": "Kinshasa" },
              "dateEnregistrement": "2025-05-28T15:30:00.000Z",
              "taxes": [ { "type": "foncière", "montant": 500, "periode": "2024", "status": "Payée", "datePaiement": "2025-05-29T11:00:00.000Z" } ]
            },
            {
              "id": "P-003",
              "proprietaire": { "nom": "Mabiala", "prenom": "Didier", "telephone": "0856789012", "email": "didier@example.com" },
              "adresse": { "avenue": "Wangata", "quartier": "Centre Ville", "commune": "Gombe", "ville": "Kinshasa" },
              "dateEnregistrement": "2025-05-20T09:00:00.000Z",
              "taxes": [ { "type": "foncière", "montant": 750, "periode": "2025", "status": "En retard", "datePaiement": null } ]
            }
          ];
        });
    },
    formatCurrency(value) {
      if (value === null || value === undefined) return '0 CDF';
      return value.toLocaleString('fr-CD', { style: 'currency', currency: 'CDF', minimumFractionDigits: 0, maximumFractionDigits: 0 });
    },
    getStatusSeverity(status) {
      switch (status) {
        case 'Payée':
          return 'success';
        case 'En attente':
          return 'warning';
        case 'En retard':
          return 'danger';
        default:
          return 'info'; // Default severity
      }
    }
  },
};
</script>

<style scoped>
/* PrimeVue will handle most of the styling. You generally won't need these specific table styles anymore. */
/* You can remove or keep them if they apply to other elements. */
</style>