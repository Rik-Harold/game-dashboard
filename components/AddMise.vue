<template>
  <div class="fixed bottom-8 right-0 left-0 text-center">
    <UModal title="Défi de combat" description="Renseignez les informations nécessaires à votre mise">
      <!-- Bouton de clic -->
      <UButton icon="i-lucide-search" size="xl" color="primary" variant="outline" />
      <!-- <UButton label="Open" color="neutral" variant="subtle" /> -->

      <template #body>
        <!-- Info combat -->
        <p>Le combat sera un combat <i>singulier</i> qui se déroulera dans une arène du groupe <i>Shinobi New Generation</i>.</p>
        
        <!-- Sélection des acteurs du combat -->
        <div class="flex justify-between my-2">
          <label for="roliste">Rôliste</label>
          <USelectMenu v-model="selectRoliste" placeholder="Sélectionnez le rôliste" :items="rolistes" class="w-2/3 md:w-1/2" />
        </div>
        <div class="flex justify-between mb-2">
          <label for="roliste">Personnage</label>
          <USelectMenu v-model="selectPersonnage" placeholder="Sélectionnez le personnage" :items="personnages" class="w-2/3 md:w-1/2" />
        </div>
        <div class="flex justify-between mb-2">
          <label for="arbitre">Arbitre</label>
          <USelectMenu v-model="selectArbitre" placeholder="Sélectionnez l'arbitre" :items="rolistes" class="w-2/3 md:w-1/2" />
        </div>
        <UTextarea disabled class="w-full" color="neutral" variant="subtle" placeholder="Saisissez le lien du combat ou de l'arène" />

        <div class="text-center my-6">
          <UInputNumber
            v-model="valeurMise"
            :min="1" :max="100"
            :format-options="{
              style: 'currency',
              currency: 'USD',
              currencyDisplay: 'code',
              currencySign: 'accounting',
            }"
          />
        </div>
        <div class="text-center mt-4">
          <UButton color="primary" @click="transactionMise(selectRoliste, selectPersonnage, selectArbitre, valeurMise)">Confirmer la mise</UButton>
        </div>
      </template>
    </UModal>
  </div>
</template>

<script>
export default {
  emits: ['startMise'],
  setup(props, { emit }) {
    // Déclaration des évènements personnalisés

    // Déclaration des variables réactives
    const rolistes = ref([
      {
        label: 'Rik',
        id: 'rik'
      },
      {
        label: 'Prince',
        id: 'prince'
      },
      {
        label: 'Aristarque',
        id: 'aristarque'
      },
      {
        label: 'Sanogo',
        id: 'sanogo'
      }
    ])
    const personnages = ref([
      {
        label: 'Uchiha Tagar',
        id: 'tagar'
      },
      {
        label: 'Uzumaki Harukai',
        id: 'harukai'
      },
      {
        label: 'Aeshisen Shiraemon',
        id: 'shiraemon'
      },
      {
        label: 'Hyuga Shiro',
        id: 'shiro'
      },
      {
        label: 'Yossin',
        id: 'yossin'
      },
      {
        label: 'Yuki Grimray',
        id: 'grimray'
      }
    ])
    const arbitres = ref([
      {
        label: 'Rik',
        id: 'rik'
      },
      {
        label: 'Prince',
        id: 'prince'
      },
      {
        label: 'Aristarque',
        id: 'aristarque'
      },
      {
        label: 'Sanogo',
        id: 'sanogo'
      }
    ])
    const selectRoliste = ref({
      label: ''
    })
    const selectPersonnage = ref({
      label: ''
    })
    const selectArbitre = ref({
      label: ''
    })
    const valeurMise = ref(1)

    function transactionMise(roliste, personnage, arbitre, valeurAMiser) {
      // Récupération des informations
      

      // Emission de l'objet de mise de combat
      emit('startMise', {
        nameRoliste: roliste,
        namePersonnage: personnage,
        nameArbitre: arbitre,
        valeurDeMise: valeurAMiser
      })
    }

    // expose l'état au template
    return {
      rolistes,
      personnages,
      arbitres,
      selectArbitre,
      selectPersonnage,
      selectRoliste,
      valeurMise,
      transactionMise
    }
  }
}
</script>

<style>

</style>