<template>
    <UContainer>
        <UNavigationMenu orientation="horizontal" :items="items" class="data-[orientation=vertical]:w-48" />
    </UContainer>
</template>

<script setup lang="ts">
import type { NavigationMenuItem } from '@nuxt/ui'

const items = ref<NavigationMenuItem[][]>([
  [
    {
      label: 'Links',
      type: 'label'
    },
    {
      label: 'Compte',
      icon: 'i-lucide-book-open',
      children: [
        {
          label: 'Profil',
          description: 'Consultez, modifiez et gérez vos informations personnelles.',
          icon: 'i-lucide-house',
          to: '/compte/profil'
        },
        {
          label: 'Transactions',
          description: 'Consulter l\'historique de vos transactions et optenez l\'état de vos gains.',
          icon: 'i-lucide-cloud-download',
          to: '/compte/transactions',
        },
        {
          label: 'Portefeuille',
          icon: 'i-lucide-smile',
          description: 'Connectez-vous via Google et consultez votre portefeuille TipLink pour gérez vos actifs.',
          to: 'https://tiplink.io/',
          target: '_blank'
        },
        {
          label: 'Déconnexion',
          icon: 'i-lucide-swatch-book',
          description: 'Déconnectez-vous et revenez plus tard.',
          to: '/'
        }
      ]
    },
    {
      label: 'Miser',
      icon: 'i-lucide-database',
      active: true,
      defaultOpen: true,
      children: [
        {
          label: 'Mise de combat',
          icon: 'i-lucide-file-text',
          description: 'Define shortcuts for your application.',
          to: '/liste-mises'
        },
        {
          label: 'Mise sur combat',
          icon: 'i-lucide-file-text',
          description: 'Display a modal/slideover within your application.',
          to: '/'
        },
        {
          label: 'Mise de tournoi',
          icon: 'i-lucide-file-text',
          description: 'Display a toast within your application.',
          to: '/'
        }
      ]
    },
    {
      label: 'Transferts',
      icon: 'i-lucide-box',
      children: [
        {
          label: 'Dépôt',
          icon: 'i-lucide-file-text',
          description: 'Alimentez votre portefeuille TipLink depuis n\'importe quel moyen de paiement.',
          to: '/transfert/depot'
        },
        {
          label: 'Retrait',
          icon: 'i-lucide-file-text',
          description: 'Retirez vos gains accumulés et recevez votre argent depuis un portefeuille mobile ou en liquide.',
          to: '/transfert/retrait'
        },
        {
          label: 'Don',
          icon: 'i-lucide-file-text',
          description: 'Soutenez la communauté en partageant vos gains afin d\'aider à développer les outils pour enrichir l\'expérience.',
          to: '/transfert/don'
        }
      ]
    }
  ],
  [
    {
      label: 'Notifications',
      icon: 'i-simple-icons-github',
      badge: '32'
    },
    {
      label: 'Aide',
      icon: 'i-lucide-circle-help',
      disabled: true
    }
  ]
])
</script>

<style>

</style>