<template>
    <header
        class="
            bg-white
            h-header
            flex flex-row
            shadow-md
            items-center
            justify-between
            px-8
            py-2
            sticky
            top-0
            z-50
        "
    >
        <div class="flex flex-row gap-6">
            <button class="p-2" @click="toggleSidebar">
                <FontAwesomeIcon :icon="icon.faBars" />
            </button>
            <button class="p-2">
                <FontAwesomeIcon :icon="icon.faUndoAlt" flip="horizontal" />
            </button>
            <button class="p-2">
                <FontAwesomeIcon :icon="icon.faHome" />
            </button>
        </div>
        <div>
            <p>Header</p>
        </div>
    </header>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useStore } from "@/store";
import { faBars, faUndoAlt, faHome } from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

const store = useStore();
const icon = computed(() => ({
    faBars,
    faUndoAlt,
    faHome,
}));

const toggleSidebar = () => store.commit("layout/toggleSidebar");
</script>
