<script setup>
import { onMounted, ref } from 'vue';

// Données de parcelles directement dans le composant pour la démo
// En production, vous feriez un appel API ici.
const parcelles = ref([
    {
        "id": "P-001",
        "adresse": {
            "avenue": "Home",
            "quartier": "Jolie Park",
            "commune": "Ngaliema",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Kalala",
            "prenom": "Christophe"
        },
        "taxes": [
            { "type": "foncière", "montant": 500, "periode": "2025", "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-002",
        "adresse": {
            "avenue": "Mboma",
            "quartier": "LDK",
            "commune": "N'Sele",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Nsimba",
            "prenom": "Isabella"
        },
        "taxes": [
            { "type": "foncière", "montant": 500, "periode": "2024", "status": "Payée", "datePaiement": "2024-02-20" }
        ]
    },
    {
        "id": "P-003",
        "adresse": {
            "avenue": "Wangata",
            "quartier": "Centre Ville",
            "commune": "Gombe",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Mabiala",
            "prenom": "Didier"
        },
        "taxes": [
            { "type": "foncière", "montant": 750, "periode": "2025", "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-004",
        "adresse": {
            "avenue": "Kasa-Vubu",
            "quartier": "Nzadi",
            "commune": "Kalamu",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Lofombo",
            "prenom": "Emilie"
        },
        "taxes": [
            { "type": "foncière", "montant": 600, "periode": "2023", "status": "En retard", "datePaiement": null }
        ]
    },
    {
        "id": "P-005",
        "adresse": {
            "avenue": "Lemba",
            "quartier": "Salongo",
            "commune": "Lemba",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Mbuyi",
            "prenom": "Marcel"
        },
        "taxes": [
            { "type": "foncière", "montant": 300, "periode": "2025", "status": "Payée", "datePaiement": "2025-06-03" }
        ]
    },
    {
        "id": "P-006",
        "adresse": {
            "avenue": "De la Paix",
            "quartier": "Quartier 1",
            "commune": "Masina",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Kabongo",
            "prenom": "Jean"
        },
        "taxes": [
            { "type": "foncière", "montant": 450, "periode": "2024", "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-007",
        "adresse": {
            "avenue": "Loi",
            "quartier": "GB",
            "commune": "Barumbu",
            "ville": "Kinshasa"
        },
        "proprietaire": {
            "nom": "Mani",
            "prenom": "Sophie"
        },
        "taxes": [
            { "type": "foncière", "montant": 800, "periode": "2025", "status": "Payée", "datePaiement": "2025-05-10" }
        ]
    }
]);

// Fonction pour formater le montant en devise locale (CDF pour Kinshasa)
function formatCurrency(value) {
    if (value === null || value === undefined) return '0 CDF';
    return value.toLocaleString('fr-CD', { style: 'currency', currency: 'CDF', minimumFractionDigits: 0, maximumFractionDigits: 0 });
}

// Fonction pour obtenir la classe de badge en fonction du statut
const getStatusSeverity = (status) => {
    switch (status) {
        case 'Payée':
            return 'success';
        case 'En attente':
            return 'warning';
        case 'En retard':
            return 'danger';
        default:
            return null;
    }
};

onMounted(() => {
    // Dans un cas réel, vous feriez un appel API ici pour charger les parcelles
    // Exemple: fetch('/api/parcelles').then(res => res.json()).then(data => parcelles.value = data);
    // Pour cette démo, les données sont déjà définies.
});
</script>

<template>
    <div class="card">
        <div class="font-semibold text-xl mb-4">Parcelles Récentes & Statut des Taxes</div>
        <DataTable :value="parcelles" :rows="5" :paginator="true" responsiveLayout="scroll">
            <Column header="ID Parcelle" field="id" style="width: 15%">
                <template #body="slotProps">
                    <span class="font-medium">{{ slotProps.data.id }}</span>
                </template>
            </Column>

            <Column header="Adresse" :sortable="true" style="width: 30%">
                <template #body="slotProps">
                    <span class="font-medium">{{ slotProps.data.adresse.quartier }}, {{ slotProps.data.adresse.commune }}</span>
                    <div class="mt-1 text-muted-color">{{ slotProps.data.adresse.ville }}</div>
                </template>
            </Column>

            <Column header="Propriétaire" :sortable="true" style="width: 20%">
                <template #body="slotProps">
                    {{ slotProps.data.proprietaire.prenom }} {{ slotProps.data.proprietaire.nom }}
                </template>
            </Column>

            <Column header="Statut Taxe" :sortable="true" style="width: 15%">
                <template #body="slotProps">
                    <Tag v-if="slotProps.data.taxes && slotProps.data.taxes.length > 0"
                        :value="slotProps.data.taxes[0].status"
                        :severity="getStatusSeverity(slotProps.data.taxes[0].status)"
                    ></Tag>
                    <span v-else>N/A</span>
                </template>
            </Column>

            <Column header="Montant Taxe" :sortable="true" style="width: 20%">
                <template #body="slotProps">
                    <span v-if="slotProps.data.taxes && slotProps.data.taxes.length > 0">
                        {{ formatCurrency(slotProps.data.taxes[0].montant) }}
                    </span>
                    <span v-else>0 CDF</span>
                </template>
            </Column>

            <Column style="width: 10%" header="Action">
                <template #body>
                    <Button icon="pi pi-search" type="button" class="p-button-text"></Button>
                </template>
            </Column>
        </DataTable>
    </div>
</template>