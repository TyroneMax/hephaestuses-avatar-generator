<script setup>
import AppFooter from "./components/AppFooter.vue";
import AppTopbar from "./components/AppTopbar.vue";
import StatsWidget from "./components/dashboard/StatsWidget.vue";
import SalesTrendWidget from "./components/dashboard/SalesTrendWidget.vue";
import RecentActivityWidget from "./components/dashboard/RecentActivityWidget.vue";
import ProductOverviewWidget from "./components/dashboard/ProductOverviewWidget.vue";
import AvatarSelector from "./components/AvatarSelector.vue";
</script>

<template>
    <div class="bg-blue-100 dark:bg-surface-950 min-h-screen p-8 flex flex-col gap-6">

        <AppTopbar />
        <div class="flex flex-col w-full max-w-7xl mx-auto gap-6 flex-1">
          <AvatarSelector></AvatarSelector>
        </div>
        <AppFooter />
    </div>
</template>
