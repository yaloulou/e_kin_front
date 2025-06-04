<script setup>
import { ref } from 'vue';

const menu = ref(null);

const items = ref([
    { label: 'Ajouter une notification', icon: 'pi pi-fw pi-plus' },
    { label: 'Supprimer', icon: 'pi pi-fw pi-trash' }
]);
</script>

<template>
    <div class="card">
        <div class="flex items-center justify-between mb-6">
            <div class="font-semibold text-xl">Notifications sur les Parcelles et Propriétaires</div>
            <div>
                <Button icon="pi pi-ellipsis-v" class="p-button-text p-button-plain p-button-rounded" @click="$refs.menu.toggle($event)"></Button>
                <Menu ref="menu" popup :model="items" class="!min-w-40"></Menu>
            </div>
        </div>

        <span class="block text-muted-color font-medium mb-4">AUJOURD'HUI</span>
        <ul class="p-0 mx-0 mt-0 mb-6 list-none">
            <li class="flex items-center py-2 border-b border-surface">
                <div class="w-12 h-12 flex items-center justify-center bg-blue-100 dark:bg-blue-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-map !text-xl text-blue-500"></i>
                </div>
                <span class="text-surface-900 dark:text-surface-0 leading-normal"
                    >La parcelle <span class="text-primary font-bold">#XYZ789</span>
                    <span class="text-surface-700 dark:text-surface-100">a été ajoutée par <span class="text-primary font-bold">Jean Dupont</span>.</span>
                </span>
            </li>
            <li class="flex items-center py-2">
                <div class="w-12 h-12 flex items-center justify-center bg-orange-100 dark:bg-orange-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-user-plus !text-xl text-orange-500"></i>
                </div>
                <span class="text-surface-700 dark:text-surface-100 leading-normal"><span class="text-primary font-bold">Marie Curie</span> a été enregistrée comme nouveau propriétaire.</span>
            </li>
        </ul>

        <span class="block text-muted-color font-medium mb-4">HIER</span>
        <ul class="p-0 m-0 list-none mb-6">
            <li class="flex items-center py-2 border-b border-surface">
                <div class="w-12 h-12 flex items-center justify-center bg-blue-100 dark:bg-blue-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-pencil !text-xl text-blue-500"></i>
                </div>
                <span class="text-surface-900 dark:text-surface-0 leading-normal"
                    >Les informations de la parcelle <span class="text-primary font-bold">#ABC123</span>
                    <span class="text-surface-700 dark:text-surface-100">ont été mises à jour par <span class="text-primary font-bold">Sophie Martin</span>.</span>
                </span>
            </li>
            <li class="flex items-center py-2 border-b border-surface">
                <div class="w-12 h-12 flex items-center justify-center bg-pink-100 dark:bg-pink-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-exclamation-triangle !text-xl text-pink-500"></i>
                </div>
                <span class="text-surface-900 dark:text-surface-0 leading-normal"
                    >Une anomalie a été détectée sur la parcelle <span class="text-primary font-bold">#DEF456</span>.
                    <span class="text-surface-700 dark:text-surface-100">Veuillez vérifier les détails.</span>
                </span>
            </li>
        </ul>
        <span class="block text-muted-color font-medium mb-4">LA SEMAINE DERNIÈRE</span>
        <ul class="p-0 m-0 list-none">
            <li class="flex items-center py-2 border-b border-surface">
                <div class="w-12 h-12 flex items-center justify-center bg-green-100 dark:bg-green-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-check-circle !text-xl text-green-500"></i>
                </div>
                <span class="text-surface-900 dark:text-surface-0 leading-normal">Le transfert de propriété pour la parcelle <span class="text-primary font-bold">#GHI789</span> a été approuvé.</span>
            </li>
            <li class="flex items-center py-2 border-b border-surface">
                <div class="w-12 h-12 flex items-center justify-center bg-purple-100 dark:bg-purple-400/10 rounded-full mr-4 shrink-0">
                    <i class="pi pi-calendar-times !text-xl text-purple-500"></i>
                </div>
                <span class="text-surface-900 dark:text-surface-0 leading-normal">Un rappel de redevance est dû pour la parcelle <span class="text-primary font-bold">#JKL012</span>.</span>
            </li>
        </ul>
    </div>
</template>