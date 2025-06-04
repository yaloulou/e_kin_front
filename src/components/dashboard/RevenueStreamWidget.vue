<script setup>
import { useLayout } from '@/layout/composables/layout';
import { onMounted, ref, watch } from 'vue';

const { getPrimary, getSurface, isDarkTheme } = useLayout();

const chartData = ref(null);
const chartOptions = ref(null);

// Vos données de parcelles enrichies avec des dates de paiement plus réalistes
const parcelles = ref([
    {
        "id": "p001",
        "adresse": { "commune": "Ngaliema" },
        "proprietaire": { "nom": "Kalala", "prenom": "Christophe" },
        "taxes": [
            { "type": "foncière", "montant": 500, "periode": "2025", "status": "Payée", "datePaiement": "2025-01-10T00:00:00.000Z" } // Q1 2025
        ]
    },
    {
        "id": "p002",
        "adresse": { "commune": "N'Sele" },
        "proprietaire": { "nom": "Nsimba", "prenom": "Isabella" },
        "taxes": [
            { "type": "foncière", "montant": 500, "periode": "2024", "status": "Payée", "datePaiement": "2024-02-20T00:00:00.000Z" } // Q1 2024
        ]
    },
    {
        "id": "p003",
        "adresse": { "commune": "Gombe" },
        "proprietaire": { "nom": "Mabiala", "prenom": "Didier" },
        "taxes": [
            { "type": "foncière", "montant": 750, "periode": "2025", "status": "Payée", "datePaiement": "2025-04-05T00:00:00.000Z" } // Q2 2025
        ]
    },
    {
        "id": "p004",
        "adresse": { "commune": "Kalamu" },
        "proprietaire": { "nom": "Lofombo", "prenom": "Emilie" },
        "taxes": [
            { "type": "foncière", "montant": 600, "periode": "2023", "status": "Payée", "datePaiement": "2023-10-15T00:00:00.000Z" } // Q4 2023
        ]
    },
    {
        "id": "p005",
        "adresse": { "commune": "Lemba" },
        "proprietaire": { "nom": "Mbuyi", "prenom": "Marcel" },
        "taxes": [
            { "type": "foncière", "montant": 300, "periode": "2025", "status": "Payée", "datePaiement": "2025-06-03T00:00:00.000Z" } // Q2 2025 (aujourd'hui!)
        ]
    },
    {
        "id": "p006",
        "adresse": { "commune": "Ngaliema" },
        "proprietaire": { "nom": "Nkosi", "prenom": "Jean" },
        "taxes": [
            { "type": "foncière", "montant": 400, "periode": "2024", "status": "Payée", "datePaiement": "2024-07-25T00:00:00.000Z" } // Q3 2024
        ]
    },
    {
        "id": "p007",
        "adresse": { "commune": "Gombe" },
        "proprietaire": { "nom": "Mbala", "prenom": "Martine" },
        "taxes": [
            { "type": "foncière", "montant": 900, "periode": "2024", "status": "Payée", "datePaiement": "2024-11-01T00:00:00.000Z" } // Q4 2024
        ]
    }
]);

// Fonction pour regrouper les paiements par trimestre et par année
function getPaymentsByQuarterAndYear() {
    const payments = {};

    parcelles.value.forEach(parcelle => {
        parcelle.taxes.forEach(tax => {
            if (tax.status === 'Payée' && tax.datePaiement) {
                const paymentDate = new Date(tax.datePaiement);
                const year = paymentDate.getFullYear();
                const month = paymentDate.getMonth(); // 0-11
                const quarter = Math.floor(month / 3) + 1; // Q1, Q2, Q3, Q4

                const key = `${year}-Q${quarter}`;
                if (!payments[key]) {
                    payments[key] = 0;
                }
                payments[key] += tax.montant;
            }
        });
    });

    // Trier les clés (ex: "2024-Q1", "2024-Q2") pour s'assurer de l'ordre chronologique
    const sortedKeys = Object.keys(payments).sort();

    // Extraire les labels et les données
    const labels = sortedKeys;
    const data = sortedKeys.map(key => payments[key]);

    return { labels, data };
}


function setChartData() {
    const documentStyle = getComputedStyle(document.documentElement);
    const { labels, data } = getPaymentsByQuarterAndYear();

    return {
        labels: labels, // Les labels seront ex: "2024-Q1", "2024-Q2"
        datasets: [
            {
                type: 'bar',
                label: 'Montant des Taxes Collectées (CDF)',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-400'), // Une seule couleur principale pour les montants collectés
                data: data, // Les montants collectés
                borderRadius: {
                    topLeft: 8,
                    topRight: 8
                },
                borderSkipped: true,
                barThickness: 32
            }
            // On peut ajouter d'autres datasets si on veut montrer 'taxes en attente' ou 'taxes en retard' par période
            // Par exemple :
            /*
            {
                type: 'bar',
                label: 'Taxes en Retard (estimation)',
                backgroundColor: documentStyle.getPropertyValue('--p-red-400'),
                data: [100, 50, 0, 200], // Exemple de données pour les taxes en retard par trimestre
                barThickness: 32
            }
            */
        ]
    };
}

function setChartOptions() {
    const documentStyle = getComputedStyle(document.documentElement);
    const borderColor = documentStyle.getPropertyValue('--surface-border');
    const textMutedColor = documentStyle.getPropertyValue('--text-color-secondary');

    return {
        maintainAspectRatio: false,
        aspectRatio: 0.8,
        plugins: {
            tooltip: {
                callbacks: {
                    label: function(context) {
                        let label = context.dataset.label || '';
                        if (label) {
                            label += ': ';
                        }
                        if (context.parsed.y !== null) {
                            label += formatCurrency(context.parsed.y); // Utilise la fonction de formatage
                        }
                        return label;
                    }
                }
            }
        },
        scales: {
            x: {
                stacked: true, // Peut être false si on n'a qu'un dataset
                ticks: {
                    color: textMutedColor
                },
                grid: {
                    color: 'transparent',
                    borderColor: 'transparent'
                }
            },
            y: {
                stacked: true, // Peut être false si on n'a qu'un dataset
                ticks: {
                    color: textMutedColor,
                    callback: function(value) {
                        return formatCurrency(value); // Formatage de l'axe Y
                    }
                },
                grid: {
                    color: borderColor,
                    borderColor: 'transparent',
                    drawTicks: false
                }
            }
        }
    };
}

// Fonction pour formater le montant en devise locale (CDF pour Kinshasa)
function formatCurrency(value) {
    if (value === null || value === undefined) return '0 CDF';
    return value.toLocaleString('fr-CD', { style: 'currency', currency: 'CDF', minimumFractionDigits: 0, maximumFractionDigits: 0 });
}


watch([getPrimary, getSurface, isDarkTheme], () => {
    chartData.value = setChartData();
    chartOptions.value = setChartOptions();
});

onMounted(() => {
    chartData.value = setChartData();
    chartOptions.value = setChartOptions();
});
</script>

<template>
    <div class="card">
        <div class="font-semibold text-xl mb-4">Flux de Revenus des Taxes Collectées</div>
        <Chart type="bar" :data="chartData" :options="chartOptions" class="h-80" />
    </div>
</template>