<script setup>
import { computed, ref } from 'vue';

const menu = ref(null);

const items = ref([
    { label: 'Ajouter Commune', icon: 'pi pi-fw pi-plus' }, // Option de menu adaptée
    { label: 'Gérer Communes', icon: 'pi pi-fw pi-cog' } // Option de menu adaptée
]);

// Vos données de parcelles (avec des communes variées pour la démo)
const parcelles = ref([
    { "id": "p001", "adresse": { "commune": "Ngaliema" } },
    { "id": "p002", "adresse": { "commune": "N'Sele" } },
    { "id": "p003", "adresse": { "commune": "Gombe" } },
    { "id": "p004", "adresse": { "commune": "Kalamu" } },
    { "id": "p005", "adresse": { "commune": "Lemba" } },
    { "id": "p006", "adresse": { "commune": "Ngaliema" } }, // Deuxième parcelle Ngaliema
    { "id": "p007", "adresse": { "commune": "Gombe" } },    // Deuxième parcelle Gombe
    { "id": "p008", "adresse": { "commune": "Mont Ngafula" } }, // Nouvelle commune
    { "id": "p009", "adresse": { "commune": "Limete" } },       // Nouvelle commune
    { "id": "p010", "adresse": { "commune": "Ngaliema" } }  // Troisième parcelle Ngaliema
]);

// Propriété calculée pour les statistiques par commune
const statsParCommune = computed(() => {
    const totalParcelles = parcelles.value.length;
    if (totalParcelles === 0) {
        return [];
    }

    const countByCommune = {};
    parcelles.value.forEach(parcelle => {
        const commune = parcelle.adresse.commune;
        countByCommune[commune] = (countByCommune[commune] || 0) + 1;
    });

    // Convertir en tableau d'objets avec pourcentage
    const result = Object.keys(countByCommune).map(commune => {
        const count = countByCommune[commune];
        const percentage = (count / totalParcelles) * 100;
        return {
            commune: commune,
            count: count,
            percentage: parseFloat(percentage.toFixed(1)) // Arrondir à une décimale
        };
    });

    // Trier par pourcentage décroissant (pour avoir les "meilleures ventes" en premier)
    return result.sort((a, b) => b.percentage - a.percentage);
});

// Couleurs pour les barres de progression (vous pouvez étendre cette liste si plus de communes)
const colors = ['orange-500', 'cyan-500', 'pink-500', 'green-500', 'purple-500', 'teal-500', 'blue-500', 'yellow-500'];

// Fonction pour obtenir une couleur basée sur l'index
function getColor(index) {
    return colors[index % colors.length];
}
</script>

<template>
    <div class="card">
        <div class="flex justify-between items-center mb-6">
            <div class="font-semibold text-xl">Répartition des Parcelles par Commune</div>
            <div>
                <Button icon="pi pi-ellipsis-v" class="p-button-text p-button-plain p-button-rounded" @click="$refs.menu.toggle($event)"></Button>
                <Menu ref="menu" popup :model="items" class="!min-w-40"></Menu>
            </div>
        </div>
        <ul class="list-none p-0 m-0">
            <li v-for="(stat, index) in statsParCommune" :key="stat.commune" class="flex flex-col md:flex-row md:items-center md:justify-between mb-6 last:mb-0">
                <div>
                    <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">{{ stat.commune }}</span>
                    <div class="mt-1 text-muted-color">{{ stat.count }} parcelle(s)</div>
                </div>
                <div class="mt-2 md:mt-0 flex items-center">
                    <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                        <div :class="`bg-${getColor(index)} h-full`" :style="{ width: stat.percentage + '%' }"></div>
                    </div>
                    <span :class="`text-${getColor(index)} ml-4 font-medium`">%{{ stat.percentage }}</span>
                </div>
            </li>
            <li v-if="statsParCommune.length === 0" class="text-muted-color text-center py-4">
                Aucune donnée de parcelle disponible.
            </li>
        </ul>
    </div>
</template>