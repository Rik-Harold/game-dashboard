<template>
  <!-- Titre de la page -->
  <h1 class="text-center bg-emerald-700 py-2 my-6 rounded-md">Miser</h1>
  <!-- Zone des mises -->
  <UContainer class="flex justify-center md:justify-around flex-wrap mb-16">
    <!-- Card de mise -->
    <UCard v-for="mise in mises" :key="mise.id" class="mb-6 mx-2" variant="soft">
      <template #header>
        <!-- <Placeholder class="h-8" /> -->
         <h3 class="text-center">Mise de combat</h3>
      </template>

      <!-- Combattants -->
      <UContainer class="text-center my-2">
        <p>@{{ mise.roliste1 }}</p>
        <p>vs</p>
        <p>@{{ mise.roliste2 }}</p>
      </UContainer>

      <!-- Informations principales -->
      <div>
        <p>Arbitre : @{{ mise.arbitre }}</p>
        <p>Groupe : {{ mise.groupe }}</p>
      </div>
  
      <template #footer>
        <div class="flex justify-center space-x-2">
          <!-- Affichage de la mise -->
          <UTooltip arrow text="Mise de combat">
            <UButton :label="mise.valeur + ' $'" color="neutral" variant="subtle" />
          </UTooltip>
          <!-- Bouton de consultation -->
          <UModal
            title="Mise sur combat"
            description="Ce combat se déroule dans le groupe " + {{ mise.valeur }}
          >
            <UButton label="Consulter" variant="subtle" />

            <template #body>
              <!-- Info combat -->
              <p>Le combat sera un combat <i>singulier</i> qui se déroulera dans une arène du groupe <i>Shinobi New Generation</i>.</p>
              <!-- Sélection des acteurs du combat -->
              <div class="flex justify-between my-2">
                <label for="roliste">Rôliste</label>
                <USelectMenu v-model="selectRoliste" placeholder="Sélectionnez le rôliste" :items="rolistes" class="w-1/2" disabled />
              </div>
              <div class="flex justify-between mb-2">
                <label for="roliste">Personnage</label>
                <USelectMenu v-model="selectPersonnage" placeholder="Sélectionnez le personnage" :items="personnages" class="w-1/2" disabled />
              </div>
              <div class="flex justify-between mb-2">
                <label for="arbitre">Arbitre</label>
                <USelectMenu v-model="selectArbitre" placeholder="Sélectionnez l'arbitre" :items="rolistes" class="w-1/2" disabled />
              </div>
              <UTextarea class="w-full" color="neutral" variant="subtle" placeholder="Saisissez le lien du combat ou de l'arène" />

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
                  disabled
                />
              </div>
              <!-- Zone de confirmation -->
              <div class="flex justify-center space-x-4 mt-4">
                <UButton color="neutral">Confirmer la mise</UButton>
                <UButton color="primary">Lancer le combat</UButton>
              </div>
            </template>
          </UModal>
        </div>
      </template>
    </UCard>
  </UContainer>

  <!-- Bouton d'ajout de mises -->
  <AddMise @start-mise="infoMise"></AddMise>
</template>

<script>
import AddMise from '../components/AddMise.vue'
definePageMeta({
  layout: 'users'
})
export default {
  components: {
    AddMise
  },
  setup() {
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
      label: 'Rik'
    })
    const selectPersonnage = ref({
      label: 'Uzumaki Harukai'
    })
    const selectArbitre = ref({
      label: 'Prince'
    })
    const mises = ref([
      {
        id: 'SHIBIRIK0001',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      },
      {
        id: 'SHIBIRIK0002',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      },
      {
        id: 'SHIBIRIK0003',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      },
      {
        id: 'SHIBIRIK0004',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      },
      {
        id: 'SHIBIRIK0005',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      },
      {
        id: 'SHIBIRIK0006',
        roliste1: "Uchiha Tagar",
        roliste2: "Senju Tiger-rama",
        groupe: 'Shinobi New Generation',
        valeur: 15,
        arbitre: 'amen',
      }
    ])
    const valeurMise = ref(5)

    function infoMise(infoDeMise) {
      console.log(infoDeMise)
      mises.value.push({
        id: 'SHIBIRIK000' + (mises.value.length+1),
        roliste1: "Karuto",
        roliste2: infoDeMise.namePersonnage.label,
        groupe: 'Shinobi New Generation',
        valeur: infoDeMise.valeurDeMise,
        arbitre: infoDeMise.nameArbitre.id,
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
      mises,
      valeurMise,
      infoMise
    }
  }
}
</script>

<style>

</style>