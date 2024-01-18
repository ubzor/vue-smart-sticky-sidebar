<template>
    <Header />

    <div class="bg-base-200">
        <div class="container mx-auto p-4 flex flex-row gap-8 py-10">
            <SmartStickySidebar
                container-class="flex-[1] min-w-48"
                content-class=""
                :top-spacing="24"
                :bottom-spacing="24"
            >
                <div class="card bg-base-100 shadow-lg">
                    <div class="card-body gap-8">
                        <Filter v-for="i in 21" :key="i" />
                    </div>
                </div>
            </SmartStickySidebar>

            <div class="flex-[3] grid grid-cols-3 gap-4">
                <Product v-for="i in 30" :key="i" />
            </div>
        </div>
    </div>

    <Footer />
</template>

<script setup lang="ts">
import SmartStickySidebar from './components/SmartStickySidebar.vue'

import Header from './components/HeaderComponent.vue'
import Footer from './components/FooterComponent.vue'
import Product from './components/ProductComponent.vue'
import Filter from './components/FilterComponent.vue'
</script>
