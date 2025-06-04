<script setup>
import { computed, ref } from 'vue';

// Données de parcelles directement dans le composant pour la démo
// En production, ces données viendraient d'une API ou d'un store.
const parcelles = ref([
    {
        "id": "P-001",
        "proprietaire": { "nom": "Kalala", "prenom": "Christophe" },
        "dateEnregistrement": "2025-06-03T10:00:00.000Z", // Enregistrée hier
        "taxes": [
            { "montant": 500, "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-002",
        "proprietaire": { "nom": "Nsimba", "prenom": "Isabella" },
        "dateEnregistrement": "2025-06-04T09:00:00.000Z", // Enregistrée aujourd'hui
        "taxes": [
            { "montant": 500, "status": "Payée", "datePaiement": "2025-06-04T10:00:00.000Z" } // Payée aujourd'hui
        ]
    },
    {
        "id": "P-003",
        "proprietaire": { "nom": "Mabiala", "prenom": "Didier" },
        "dateEnregistrement": "2025-05-28T14:00:00.000Z",
        "taxes": [
            { "montant": 750, "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-004",
        "proprietaire": { "nom": "Lofombo", "prenom": "Emilie" },
        "dateEnregistrement": "2025-05-15T08:00:00.000Z",
        "taxes": [
            { "montant": 600, "status": "En retard", "datePaiement": null }
        ]
    },
    {
        "id": "P-005",
        "proprietaire": { "nom": "Mbuyi", "prenom": "Marcel" },
        "dateEnregistrement": "2025-06-04T11:30:00.000Z", // Enregistrée aujourd'hui
        "taxes": [
            { "montant": 300, "status": "Payée", "datePaiement": "2025-06-04T12:00:00.000Z" } // Payée aujourd'hui
        ]
    },
    {
        "id": "P-006",
        "proprietaire": { "nom": "Kalala", "prenom": "Christophe" }, // Même propriétaire que P-001
        "dateEnregistrement": "2025-06-02T09:00:00.000Z",
        "taxes": [
            { "montant": 450, "status": "En attente", "datePaiement": null }
        ]
    },
    {
        "id": "P-007",
        "proprietaire": { "nom": "Kankonde", "prenom": "Alice" },
        "dateEnregistrement": "2025-06-04T07:00:00.000Z", // Enregistrée aujourd'hui
        "taxes": [
            { "montant": 800, "status": "Payée", "datePaiement": "2025-06-04T07:30:00.000Z" } // Payée aujourd'hui
        ]
    },
     {
        "id": "P-008",
        "proprietaire": { "nom": "Mutombo", "prenom": "Andre" },
        "dateEnregistrement": "2025-05-30T10:00:00.000Z",
        "taxes": [
            { "montant": 250, "status": "En retard", "datePaiement": null }
        ]
    }
]);

// Fonction pour formater le montant en devise locale (CDF pour Kinshasa)
function formatCurrency(value) {
    if (value === null || value === undefined) return '0 CDF';
    return value.toLocaleString('fr-CD', { style: 'currency', currency: 'CDF', minimumFractionDigits: 0, maximumFractionDigits: 0 });
}

// --- Calculs pour les statistiques des cartes ---

// Total Parcelles Enregistrées
const totalParcelles = computed(() => parcelles.value.length);

// Nouvelles Parcelles Enregistrées (aujourd'hui)
const newParcellesToday = computed(() => {
    const today = new Date();
    today.setHours(0, 0, 0, 0); // Début de journée

    return parcelles.value.filter(p => {
        const registrationDate = new Date(p.dateEnregistrement);
        registrationDate.setHours(0, 0, 0, 0);
        return registrationDate.getTime() === today.getTime();
    }).length;
});

// Montant Total des Taxes Collectées
const totalTaxesCollected = computed(() => {
    let total = 0;
    parcelles.value.forEach(parcelle => {
        parcelle.taxes.forEach(tax => {
            if (tax.status === 'Payée') {
                total += tax.montant;
            }
        });
    });
    return total;
});

// Augmentation de la collecte de taxes (exemple : depuis la semaine dernière)
const taxIncreasePercentage = computed(() => {
    // Ceci est une simulation. En réalité, vous auriez besoin de données historiques.
    // Pour l'exemple, nous allons juste simuler un pourcentage fixe ou basé sur une logique simple.
    // Supposons une augmentation de 15% pour la démo.
    // Ou un calcul basé sur les paiements d'aujourd'hui vs. ceux de la semaine dernière.
    const today = new Date();
    today.setHours(0, 0, 0, 0);

    const sevenDaysAgo = new Date(today);
    sevenDaysAgo.setDate(today.getDate() - 7);

    let collectedToday = 0;
    let collectedLastWeek = 0; // Taxes payées dans les 7 derniers jours (incluant aujourd'hui)

    parcelles.value.forEach(parcelle => {
        parcelle.taxes.forEach(tax => {
            if (tax.status === 'Payée' && tax.datePaiement) {
                const paymentDate = new Date(tax.datePaiement);
                paymentDate.setHours(0, 0, 0, 0);

                if (paymentDate.getTime() === today.getTime()) {
                    collectedToday += tax.montant;
                }
                if (paymentDate >= sevenDaysAgo && paymentDate <= today) {
                    collectedLastWeek += tax.montant;
                }
            }
        });
    });

    // Pour obtenir une comparaison "depuis la semaine dernière", nous pouvons comparer
    // la collecte de cette semaine avec celle de la semaine précédente.
    // Simplification pour l'exemple : si collectedLastWeek est supérieur à 0,
    // on simule une augmentation en pourcentage.
    if (collectedLastWeek > 0) {
        // Supposons qu'on compare la collecte d'aujourd'hui avec une moyenne passée ou un objectif
        // Ici, nous allons juste afficher un pourcentage positif si des paiements ont eu lieu.
        return ((collectedToday / (collectedLastWeek || 1)) * 100).toFixed(0); // Exemple simple
    }
    return '0'; // Pas de changement ou pas de données
});


// Nombre de Propriétaires Uniques
const uniqueOwners = computed(() => {
    const ownerSet = new Set();
    parcelles.value.forEach(parcelle => {
        if (parcelle.proprietaire) {
            ownerSet.add(`${parcelle.proprietaire.nom} ${parcelle.proprietaire.prenom}`);
        }
    });
    return ownerSet.size;
});

// Nouveaux Propriétaires Enregistrés (aujourd'hui)
const newOwnersToday = computed(() => {
    const today = new Date();
    today.setHours(0, 0, 0, 0);

    const ownersToday = new Set();
    const allPreviousOwners = new Set();

    parcelles.value.forEach(p => {
        const registrationDate = new Date(p.dateEnregistrement);
        registrationDate.setHours(0, 0, 0, 0);
        const ownerId = `${p.proprietaire.nom} ${p.proprietaire.prenom}`;

        if (registrationDate.getTime() === today.getTime()) {
            ownersToday.add(ownerId);
        } else {
            allPreviousOwners.add(ownerId);
        }
    });

    let countNew = 0;
    ownersToday.forEach(owner => {
        if (!allPreviousOwners.has(owner)) {
            countNew++;
        }
    });
    return countNew;
});


// Taxes en Retard (Non Payées)
const overdueTaxesCount = computed(() => {
    let count = 0;
    parcelles.value.forEach(parcelle => {
        parcelle.taxes.forEach(tax => {
            if (tax.status === 'En retard') {
                count++;
            }
        });
    });
    return count;
});

// Taxes "répondus" (ici, nous pouvons interpréter comme "Taxes Payées aujourd'hui" pour l'analogie)
const paidTaxesToday = computed(() => {
    const today = new Date();
    today.setHours(0, 0, 0, 0);

    let count = 0;
    parcelles.value.forEach(parcelle => {
        parcelle.taxes.forEach(tax => {
            if (tax.status === 'Payée' && tax.datePaiement) {
                const paymentDate = new Date(tax.datePaiement);
                paymentDate.setHours(0, 0, 0, 0);
                if (paymentDate.getTime() === today.getTime()) {
                    count++;
                }
            }
        });
    });
    return count;
});

</script>

<template>
    <div class="col-span-12 lg:col-span-6 xl:col-span-3">
        <div class="card mb-0">
            <div class="flex justify-between mb-4">
                <div>
                    <span class="block text-muted-color font-medium mb-4">Parcelles Enregistrées</span>
                    <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">{{ totalParcelles }}</div>
                </div>
                <div class="flex items-center justify-center bg-blue-100 dark:bg-blue-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                    <i class="pi pi-map text-blue-500 !text-xl"></i>
                </div>
            </div>
            <span class="text-primary font-medium">{{ newParcellesToday }} nouvelles </span>
            <span class="text-muted-color">aujourd'hui</span>
        </div>
    </div>
    <div class="col-span-12 lg:col-span-6 xl:col-span-3">
        <div class="card mb-0">
            <div class="flex justify-between mb-4">
                <div>
                    <span class="block text-muted-color font-medium mb-4">Taxes Collectées</span>
                    <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">{{ formatCurrency(totalTaxesCollected) }}</div>
                </div>
                <div class="flex items-center justify-center bg-orange-100 dark:bg-orange-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                    <i class="pi pi-wallet text-orange-500 !text-xl"></i>
                </div>
            </div>
            <span class="text-primary font-medium">{{ taxIncreasePercentage }}% </span>
            <span class="text-muted-color">depuis la semaine dernière</span>
        </div>
    </div>
    <div class="col-span-12 lg:col-span-6 xl:col-span-3">
        <div class="card mb-0">
            <div class="flex justify-between mb-4">
                <div>
                    <span class="block text-muted-color font-medium mb-4">Propriétaires Uniques</span>
                    <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">{{ uniqueOwners }}</div>
                </div>
                <div class="flex items-center justify-center bg-cyan-100 dark:bg-cyan-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                    <i class="pi pi-users text-cyan-500 !text-xl"></i>
                </div>
            </div>
            <span class="text-primary font-medium">{{ newOwnersToday }} </span>
            <span class="text-muted-color">nouveaux inscrits aujourd'hui</span>
        </div>
    </div>
    <div class="col-span-12 lg:col-span-6 xl:col-span-3">
        <div class="card mb-0">
            <div class="flex justify-between mb-4">
                <div>
                    <span class="block text-muted-color font-medium mb-4">Taxes en Retard</span>
                    <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">{{ overdueTaxesCount }}</div>
                </div>
                <div class="flex items-center justify-center bg-red-100 dark:bg-red-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                    <i class="pi pi-exclamation-triangle text-red-500 !text-xl"></i>
                </div>
            </div>
            <span class="text-primary font-medium">{{ paidTaxesToday }} </span>
            <span class="text-muted-color">payées aujourd'hui</span>
        </div>
    </div>
</template>