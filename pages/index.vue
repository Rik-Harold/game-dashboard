<template>
  <UContainer class="sm:w-2/3 md:w-1/2">

    <!-- Section de connexion/inscription -->
    <UTabs :items="items" variant="link" class="gap-4 w-full" :ui="{ trigger: 'flex-1' }">
      <template #account="{ item }">
        <p class="text-(--ui-text-muted) mb-4">
          {{ item.description }}
        </p>

        <!-- Formulaire de connexion -->
        <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
          <UFormField label="Email" name="email" required>
            <UInput v-model="state.email" class="w-full" />
          </UFormField>

          <UFormField label="Mot de passe" name="password" required>
            <UInput v-model="state.password" type="password" class="w-full" />
          </UFormField>

          <UButton type="submit" to="/liste-mises" variant="soft">
            Connexion
          </UButton>
        </UForm>
      </template>
  
      <!-- Formulaire d'inscription -->
      <template #password="{ item }">
        <p class="text-(--ui-text-muted) mb-4">
          {{ item.description }}
        </p>
  
        <UForm :schema="schema" :state="state" class="flex flex-col gap-4" @submit="onSubmit">
          <UFormField label="Nom" name="nom" required>
            <UInput v-model="state.nom" type="text" required class="w-full" />
          </UFormField>
          <UFormField label="Prénom(s)" name="prenom" required>
            <UInput v-model="state.prenom" type="text" required class="w-full" />
          </UFormField>
          <UFormField label="Pseudo" name="pseudo" required>
            <UInput v-model="state.pseudo" type="text" required class="w-full" />
          </UFormField>
          <UFormField label="Identifiant de Wallet" name="wallet" required>
            <UInput v-model="state.wallet" type="text" required class="w-full" />
          </UFormField>
          <UFormField label="New Password" name="new" required>
            <UInput v-model="state.newPassword" type="password" required class="w-full" />
          </UFormField>
          <UFormField label="Confirm Password" name="confirm" required>
            <UInput v-model="state.confirmPassword" type="password" required class="w-full" />
          </UFormField>
  
          <UButton label="Inscription" to="/liste-mises" variant="soft" class="self-end" />
        </UForm>
      </template>
    </UTabs>
  </UContainer>
</template>

<script setup lang="ts">
import * as z from 'zod'
import type { FormSubmitEvent, TabsItem } from '@nuxt/ui'

const items = [
  {
    label: 'Connexion',
    description: 'Déjà un compte ? Connectez-vous et profitez de l\'expérience.',
    icon: 'i-lucide-user',
    slot: 'account' as const
  },
  {
    label: 'Inscription',
    description: 'Renseignez vos informations et rejoignez-nous pour profitez de l\'expérience.',
    icon: 'i-lucide-lock',
    slot: 'password' as const
  }
] satisfies TabsItem[]

// Schéma de validation des champs de saisie
const schema = z.object({
  email: z.string().email('Email invalide'),
  password: z.string().min(8, 'Au moins 8 caractères'),
  nom: z.string().min(2, 'Au moins 8 caractères'),
  prenom: z.string().min(3, 'Au moins 8 caractères'),
  pseudo: z.string().min(3, 'Au moins 8 caractères'),
  wallet: z.string().min(8, 'Au moins 8 caractères'),
  newPassword: z.string().min(8, 'Au moins 8 caractères'),
  confirmPassword: z.string().min(8, 'Au moins 8 caractères')
})

type Schema = z.output<typeof schema>

const state = reactive<Partial<Schema>>({
  email: undefined,
  password: undefined,
  nom: '',
  prenom: '',
  pseudo: '',
  wallet: '',
  newPassword: '',
  confirmPassword: ''
})

const toast = useToast()
async function onSubmit(event: FormSubmitEvent<Schema>) {
  // toast.add({ title: 'Success', description: 'Le formulaire a été soumis!', color: 'success' })
  // console.log(event.data)
  navigateTo('/liste-mises')
}

</script>

<style>

</style>