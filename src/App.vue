<template>
    <div class="app">
        <div v-if="$route.path === '/admin'" class=""> <inventoryAdmin/></div>
        <div v-if="$route.path === '/'" class="commit"><inventoryCommit/></div>
        <div v-if="$route.path === '/ReCommit'" class="commit"><ReInvenrotyCommit/></div>
        <div v-if="$route.path === '/ReAdmin'" class=""> <ReinventoryAdmin/></div>

        <div v-if="$route.path === '/subCommit'" class="commit"><suborderCommit/></div>
        <div v-if="$route.path === '/subAdmin'" class=""> <suborderAdmin/></div>
        <div v-if="$route.path === '/subAdmin'" class=""> <suborderAdmin/></div>
        <div v-if="$route.path === '/excel'" class=""> <excelsheet/></div>
        <div v-if="$route.path === '/HCinco'" class="commit"><HCinco/></div>
        <div v-if="$route.path === '/HCincoadmin'"><HCincoad/></div>
        <div v-if="$route.path === '/HCcommit'" class="commit"><HCcommit/></div>
        <div v-if="$route.path === '/HCadmin'" class=""> <HCAdmin/></div>
        <div v-if="$route.path === '/YCcommit'" class=""> <YCcommit/></div>
        <div v-if="$route.path === '/YCcheck'" class=""> <YCcheck/></div>
        <div v-if="$route.path === '/YCadd'" class=""> <YCadd/></div>
        <div v-if="$route.path === '/YCsub'" class=""> <YCsub/></div>
        <div v-if="$route.path === '/YCadmin'" class=""> <YCadmin/></div>

    </div>
</template>
<script setup>
    import { useRoute } from 'vue-router'
    import { ref } from 'vue'
    import inventoryAdmin from './component/inventoryAdmin.vue'
    import inventoryCommit from './component/inventoryCommit.vue'
    import suborderCommit from './component/suborderCommit.vue'
    import suborderAdmin from './component/suborderAdmin.vue'
    import excelsheet from './component/excel.vue'
    import ReInvenrotyCommit from './component/ReInvenrotyCommit.vue'
    import ReinventoryAdmin from './component/ReinventoryAdmin.vue'
    import HCcommit from './component/HCcommit.vue'
    import HCAdmin from './component/HCdmin.vue'
    import YCcommit from './component/YCcommit.vue'
    import YCcheck from './component/YC/YCcheck.vue'
    import YCadd from './component/YC/YCadd.vue'
    import YCsub from './component/YC/YCsub.vue'
    import YCadmin from './component/YCadmin.vue'
    import HCinco from './component/HCinventoryCommit.vue'
    import HCincoad from './component/HCinventoryAdmin.vue'


    const route = useRoute()
</script>

<style scoped>
    .app {
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        /* background-color: aqua; */
    }
    .commit {
      width: 100%;
      height: 88vh;
    }
</style>
