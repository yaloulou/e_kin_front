<script setup>
import BestSellingWidget from '@/components/dashboard/BestSellingWidget.vue'; // Renommé en "Statistiques Parcelle par Commune" ou similaire
import NotificationsWidget from '@/components/dashboard/NotificationsWidget.vue'; // Activités des Parcelles
import RecentSalesWidget from '@/components/dashboard/RecentSalesWidget.vue'; // Liste des Parcelles avec Taxe
import RevenueStreamWidget from '@/components/dashboard/RevenueStreamWidget.vue'; // Statistiques des Taxes par Période
import StatsWidget from '@/components/dashboard/StatsWidget.vue'; // Cartes de Statistiques Globales
</script>

<template>
    <div class="grid grid-cols-12 gap-8">
        <div class="col-span-12">
            <StatsWidget />
        </div>

        <div class="col-span-12 lg:col-span-8">
            <RecentSalesWidget />
            <BestSellingWidget class="mt-8" />
        </div>

        <div class="col-span-12 lg:col-span-4">
            <RevenueStreamWidget />
            <NotificationsWidget class="mt-8" />
        </div>
    </div>
</template>