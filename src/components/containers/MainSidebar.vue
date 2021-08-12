<template>
    <section
        class="
            w-0
            bg-primary-50
            md:w-sidebar-expand
            h-screen
            sticky
            top-0
            flex-shrink-0
            transition-all
            duration-700
            ease-in-out
            flex flex-col
            overflow-hidden
        "
        :class="{ collapse: store.state.layout.isSidebarCollapse }"
    >
        <div
            class="
                h-header
                shadow-md
                flex
                items-center
                justify-center
                text-primary-100
                py-2
                px-12
            "
        >
            <img src="@/assets/logo/logo-corrad-white.svg" alt="Corrad" />
        </div>
        <nav class="py-4 px-2 flex-1">
            <!-- First level navigation -->
            <ul class="flex flex-col gap-1 text-white">
                <li v-for="menu in menuList" :key="menu.id">
                    <button
                        :data-active="currentRoute === menu.path"
                        @click="changeFocusedMenu(menu.path || menu.id)"
                    >
                        <div class="w-6 h-6 flex justify-center items-center">
                            <font-awesome-icon
                                :icon="menu.icon"
                                flip="horizontal"
                            />
                        </div>
                        <p
                            class="flex-1"
                            v-if="!store.state.layout.isSidebarCollapse"
                        >
                            {{ menu.label }}
                        </p>
                        <div
                            class="w-6 h-6 flex justify-center items-center"
                            v-if="menu.subMenu"
                        >
                            <font-awesome-icon
                                :icon="icon.faChevronLeft"
                                flip="horizontal"
                            />
                        </div>
                    </button>
                    <!-- Second level navigation -->
                    <ul
                        v-if="menu.subMenu"
                        class="
                            max-h-0
                            overflow-hidden
                            transition-all
                            duration-200
                        "
                        :class="{
                            focused:
                                state.focusedMenu === menu.id ||
                                menu.subMenu
                                    .map((subMenuItem) => subMenuItem.id)
                                    .includes(state.focusedMenu) ||
                                flatMapRoute[menu.id].includes(currentRoute),
                        }"
                    >
                        <li v-for="subMenu1 in menu.subMenu" :key="subMenu1.id">
                            <button
                                @click="
                                    changeFocusedMenu(
                                        subMenu1.path || subMenu1.id
                                    )
                                "
                                :data-active="currentRoute === subMenu1.path"
                            >
                                <p
                                    class="pl-8 flex-1"
                                    v-if="!store.state.layout.isSidebarCollapse"
                                >
                                    {{ subMenu1.label }}
                                </p>
                                <div
                                    class="
                                        w-6
                                        h-6
                                        flex
                                        justify-center
                                        items-center
                                        pr-4
                                    "
                                    v-if="subMenu1.subMenu"
                                >
                                    <font-awesome-icon
                                        :icon="icon.faChevronLeft"
                                        flip="horizontal"
                                        transform="shrink-4"
                                    />
                                </div>
                            </button>
                            <!-- Third level navigation -->
                            <ul
                                v-if="subMenu1.subMenu"
                                class="
                                    max-h-0
                                    overflow-hidden
                                    transition-all
                                    duration-200
                                "
                                :class="{
                                    focused:
                                        state.focusedMenu === subMenu1.id ||
                                        flatMapRoute[subMenu1.id].includes(
                                            currentRoute
                                        ),
                                }"
                            >
                                <li
                                    v-for="subMenu2 in subMenu1.subMenu"
                                    :key="subMenu2.id"
                                >
                                    <button
                                        @click="
                                            changeFocusedMenu(subMenu2.path)
                                        "
                                        :data-active="
                                            currentRoute === subMenu2.path
                                        "
                                    >
                                        <p
                                            class="pl-16"
                                            v-if="
                                                !store.state.layout
                                                    .isSidebarCollapse
                                            "
                                        >
                                            {{ subMenu2.label }}
                                        </p>
                                    </button>
                                </li>
                            </ul>
                        </li>
                    </ul>
                </li>
            </ul>
        </nav>
    </section>
</template>

<script lang="ts">
import { defineComponent, computed, reactive } from "vue";
import { useStore } from "@/store";
import {
    faTachometerAlt,
    faClipboardList,
    faUser,
    faBox,
    faChevronDown,
    faChevronLeft,
    faMousePointer,
    IconDefinition,
    faInfo,
} from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { useRouter } from "vue-router";

type MenuItem = {
    id: string;
    label: string;
    icon?: IconDefinition;
    path?: string;
    subMenu?: MenuList;
};

type MenuList = Array<MenuItem>;

type State = {
    focusedMenu?: string;
};

export default defineComponent({
    name: "MainSidebar",
    props: {},
    components: {
        FontAwesomeIcon,
    },
    setup() {
        const router = useRouter();
        const store = useStore();
        const icon = computed(() => ({
            faTachometerAlt,
            faClipboardList,
            faChevronDown,
            faChevronLeft,
            faMousePointer,
            faUser,
            faBox,
            faInfo,
        }));

        const currentRoute = router.currentRoute.value.fullPath;
        const state = reactive<State>({
            focusedMenu: undefined,
        });

        const changeFocusedMenu = (selectedPath: string) => {
            if (selectedPath.startsWith("/")) {
                return router.push(selectedPath);
            }

            if (state.focusedMenu !== selectedPath)
                state.focusedMenu = selectedPath;
            else state.focusedMenu = undefined;
        };

        // Only 3 level navigation is allowed
        const menuList: MenuList = [
            {
                id: "menu-dashboard",
                label: "Dashboard",
                icon: icon.value.faTachometerAlt,
                path: "/",
            },
            {
                id: "menu-basic-component",
                label: "Basics",
                icon: icon.value.faMousePointer,
                subMenu: [
                    {
                        id: "menu-button",
                        label: "Buttons",
                        path: "/basics/buttons",
                    },
                ],
            },
            {
                id: "menu-forms",
                label: "Forms",
                icon: icon.value.faClipboardList,
                subMenu: [
                    {
                        id: "menu-input",
                        label: "Input",
                        path: "/forms/input",
                    },
                ],
            },
            {
                id: "menu-containers",
                label: "Containers",
                icon: icon.value.faBox,
                subMenu: [
                    {
                        id: "menu-cards",
                        label: "Cards",
                        path: "/containers/cards",
                    },
                ],
            },
            {
                id: "menu-account",
                label: "Account",
                icon: icon.value.faUser,
                subMenu: [
                    {
                        id: "menu-profile",
                        label: "Profile",
                        path: "/test1",
                    },
                    {
                        id: "menu-appearance",
                        label: "Appearance",
                        path: "/test2",
                    },
                    {
                        id: "menu-domain",
                        label: "Domain",
                        subMenu: [
                            {
                                id: "menu-domain-manage",
                                label: "Manage",
                                path: "/test3",
                            },
                            {
                                id: "menu-domain-add",
                                label: "Add",
                                path: "/test4",
                            },
                        ],
                    },
                ],
            },
            {
                id: "menu-about",
                label: "About",
                icon: icon.value.faInfo,
                path: "/about",
            },
        ];

        const flatMapRoute: {
            [key: string]: Array<unknown>;
        } = {};

        menuList.forEach((menuItem) => {
            flatMapRoute[menuItem.id] = [
                menuItem.path ||
                    menuItem.subMenu?.flatMap((sub1Item) => {
                        flatMapRoute[sub1Item.id] = [
                            sub1Item.path,
                            sub1Item.subMenu?.map((sub2Item) => sub2Item.path),
                        ]
                            .flatMap((item) => item)
                            .filter(Boolean);
                        return [
                            sub1Item.path,
                            sub1Item.subMenu?.map((sub2Item) => sub2Item.path),
                        ]
                            .flatMap((item) => item)
                            .filter(Boolean);
                    }),
            ].flatMap((item) => item);
        });

        return {
            state,
            icon,
            store,
            menuList,
            router,
            currentRoute,
            flatMapRoute,
            changeFocusedMenu,
        };
    },
});
</script>

<style scoped>
.collapse {
    @apply w-sidebar-collapse;
}

button {
    @apply h-12 w-full text-left px-4 py-2 rounded-md flex flex-row gap-2 items-center hover:bg-primary-25 focus:bg-primary-25;
}

button[data-active="true"] {
    @apply bg-primary-75;
}

/* button:focus ~ ul {
    @apply max-h-screen;
} */

ul.focused {
    @apply max-h-screen;
}
</style>