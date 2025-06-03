<template>
  <div class="p-6">
    <h1 class="text-2xl font-bold mb-4">Liste des Parcelles</h1>
    <table class="min-w-full border border-gray-300">
      <thead class="bg-gray-100">
        <tr>
          <th class="border p-2">Propriétaire</th>
          <th class="border p-2">Contact</th>
          <th class="border p-2">Adresse</th>
          <th class="border p-2">Type Taxe</th>
          <th class="border p-2">Montant</th>
          <th class="border p-2">Période</th>
          <th class="border p-2">Statut</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(parcelle, index) in parcelles" :key="index" class="hover:bg-gray-50">
          <td class="border p-2">{{ parcelle.proprietaire.prenom }} {{ parcelle.proprietaire.nom }}</td>
          <td class="border p-2">
            📞 {{ parcelle.proprietaire.telephone }}<br />
            ✉️ {{ parcelle.proprietaire.email }}
          </td>
          <td class="border p-2">
            {{ parcelle.adresse.avenue }}, {{ parcelle.adresse.quartier }},
            {{ parcelle.adresse.commune }}, {{ parcelle.adresse.ville }}
          </td>
          <td class="border p-2">{{ parcelle.taxes[0].type }}</td>
          <td class="border p-2">{{ parcelle.taxes[0].montant }} USD</td>
          <td class="border p-2">{{ parcelle.taxes[0].periode }}</td>
          <td class="border p-2">{{ parcelle.taxes[0].status }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "ParcellesTable",
  data() {
    return {
      parcelles: [],
    };
  },
  mounted() {
    axios
      .get("https://plankton-app-fjjah.ondigitalocean.app/parcelles/parcelles_details")
      .then((response) => {
        this.parcelles = response.data;
      })
      .catch((error) => {
        console.error("Erreur lors du chargement des données :", error);
      });
  },
};
</script>

<style scoped>
table {
  border-collapse: collapse;
}
th, td {
  text-align: left;
}
</style>
