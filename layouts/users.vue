<template>
    <div>
        <!-- Barre de navigation -->
        <nav-user></nav-user>
        <!-- Contenu de pages -->
        <UContainer>
            <slot />
        </UContainer>
    </div>
</template>

<script>
import NavUser from '../components/nav/NavUser.vue'
export default {
    components: {
        NavUser
    }
}
</script>

<style></style>