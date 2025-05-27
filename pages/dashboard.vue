<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900">
    <!-- Header avec titre et statistiques -->
    <div class="bg-gradient-to-r from-emerald-600 to-teal-600 shadow-lg">
      <UContainer class="py-8">
        <div class="text-center text-white">
          <h1 class="text-4xl font-bold mb-2">Combat Arena</h1>
          <p class="text-emerald-100">Défis de combat en cours</p>
          <div class="flex justify-center gap-8 mt-6">
            <div class="text-center">
              <div class="text-2xl font-bold">{{ combatsStats.total }}</div>
              <div class="text-sm text-emerald-100">Total</div>
            </div>
            <div class="text-center">
              <div class="text-2xl font-bold text-yellow-300">{{ combatsStats.enCours }}</div>
              <div class="text-sm text-emerald-100">En cours</div>
            </div>
            <div class="text-center">
              <div class="text-2xl font-bold text-green-300">{{ combatsStats.termines }}</div>
              <div class="text-sm text-emerald-100">Terminés</div>
            </div>
          </div>
        </div>
      </UContainer>
    </div>

    <UContainer class="py-8">
      <!-- Filtres et recherche -->
      <div class="bg-white/5 backdrop-blur-sm rounded-2xl p-6 mb-8 border border-white/10">
        <div class="flex flex-col md:flex-row gap-4 items-center justify-between">
          <div class="flex gap-4 flex-wrap">
            <UButton
              v-for="filter in filters"
              :key="filter.key"
              :label="filter.label"
              :color="activeFilter === filter.key ? 'primary' : 'gray'"
              :variant="activeFilter === filter.key ? 'solid' : 'ghost'"
              @click="setFilter(filter.key)"
              class="transition-all duration-200"
            />
          </div>
          <div class="flex gap-4 items-center">
            <UInput
              v-model="searchQuery"
              placeholder="Rechercher un combat..."
              icon="i-lucide-search"
              class="w-64"
            />
            <USelect
              v-model="sortBy"
              :options="sortOptions"
              placeholder="Trier par"
              class="w-40"
            />
          </div>
        </div>
      </div>

      <!-- Liste des combats -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-20">
        <div
          v-for="mise in filteredMises"
          :key="mise.id"
          class="group relative bg-gradient-to-br from-slate-800/90 to-slate-900/90 backdrop-blur-sm rounded-2xl border border-slate-700/50 hover:border-emerald-500/50 transition-all duration-300 hover:transform hover:scale-105 hover:shadow-2xl hover:shadow-emerald-500/10"
        >
          <!-- Badge de statut -->
          <div class="absolute -top-2 -right-2 z-10">
            <UBadge
              :color="getStatusColor(mise.statut)"
              :label="mise.statut"
              size="sm"
              class="font-semibold"
            />
          </div>

          <div class="p-6">
            <!-- Header du combat -->
            <div class="text-center mb-6">
              <h3 class="text-xl font-bold text-white mb-2">Combat {{ mise.typeCombat }}</h3>
              <div class="text-emerald-400 font-mono text-sm">{{ mise.id }}</div>
            </div>

            <!-- Adversaires -->
            <div class="bg-black/20 rounded-xl p-4 mb-6">
              <div class="text-center space-y-3">
                <div class="flex items-center justify-center gap-3">
                  <UAvatar :alt="mise.roliste1" size="sm" />
                  <div class="text-center">
                    <div class="text-white font-semibold">{{ mise.roliste1 }}</div>
                    <div class="text-slate-400 text-xs">{{ mise.personnage1 || 'Personnage 1' }}</div>
                  </div>
                </div>
                
                <div class="flex items-center justify-center">
                  <div class="bg-gradient-to-r from-red-500 to-orange-500 text-white px-3 py-1 rounded-full text-sm font-bold animate-pulse">
                    VS
                  </div>
                </div>
                
                <div class="flex items-center justify-center gap-3">
                  <UAvatar :alt="mise.roliste2" size="sm" />
                  <div class="text-center">
                    <div class="text-white font-semibold">{{ mise.roliste2 }}</div>
                    <div class="text-slate-400 text-xs">{{ mise.personnage2 || 'Personnage 2' }}</div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Informations du combat -->
            <div class="space-y-3 mb-6">
              <div class="flex items-center gap-2 text-slate-300">
                <UIcon name="i-lucide-gavel" class="text-emerald-400" />
                <span class="text-sm">Arbitre: <span class="text-white">@{{ mise.arbitre }}</span></span>
              </div>
              <div class="flex items-center gap-2 text-slate-300">
                <UIcon name="i-lucide-users" class="text-emerald-400" />
                <span class="text-sm">{{ mise.groupe }}</span>
              </div>
              <div class="flex items-center gap-2 text-slate-300">
                <UIcon name="i-lucide-clock" class="text-emerald-400" />
                <span class="text-sm">{{ formatDate(mise.dateCreation) }}</span>
              </div>
            </div>

            <!-- Mise et actions -->
            <div class="space-y-4">
              <div class="text-center">
                <div class="bg-gradient-to-r from-emerald-600 to-teal-600 text-white px-4 py-2 rounded-xl font-bold text-lg">
                  {{ mise.valeur }} USD
                </div>
              </div>

              <div class="flex gap-2">
                <UButton
                  label="Consulter"
                  color="primary"
                  variant="outline"
                  size="sm"
                  class="flex-1"
                  @click="openCombatModal(mise)"
                />
                <UButton
                  v-if="mise.statut === 'En attente'"
                  label="Participer"
                  color="emerald"
                  size="sm"
                  class="flex-1"
                  @click="participerCombat(mise.id)"
                />
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Message si aucun combat -->
      <div v-if="filteredMises.length === 0" class="text-center py-16">
        <UIcon name="i-lucide-sword" class="text-6xl text-slate-600 mb-4" />
        <h3 class="text-xl font-semibold text-slate-400 mb-2">Aucun combat trouvé</h3>
        <p class="text-slate-500">Créez un nouveau défi pour commencer à combattre !</p>
      </div>
    </UContainer>

    <!-- Modal de consultation de combat -->
    <UModal v-model="showCombatModal" :ui="{ width: 'sm:max-w-2xl' }">
      <div class="p-6">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-2xl font-bold">Détails du Combat</h3>
          <UBadge :color="getStatusColor(selectedCombat?.statut)" :label="selectedCombat?.statut" />
        </div>

        <div v-if="selectedCombat" class="space-y-6">
          <!-- Informations générales -->
          <div class="bg-slate-50 rounded-lg p-4">
            <h4 class="font-semibold mb-3">Informations générales</h4>
            <div class="grid grid-cols-2 gap-4 text-sm">
              <div>
                <span class="text-slate-600">ID Combat:</span>
                <span class="ml-2 font-mono">{{ selectedCombat.id }}</span>
              </div>
              <div>
                <span class="text-slate-600">Type:</span>
                <span class="ml-2">{{ selectedCombat.typeCombat }}</span>
              </div>
              <div>
                <span class="text-slate-600">Groupe:</span>
                <span class="ml-2">{{ selectedCombat.groupe }}</span>
              </div>
              <div>
                <span class="text-slate-600">Arbitre:</span>
                <span class="ml-2">@{{ selectedCombat.arbitre }}</span>
              </div>
            </div>
          </div>

          <!-- Combattants -->
          <div class="bg-slate-50 rounded-lg p-4">
            <h4 class="font-semibold mb-3">Combattants</h4>
            <div class="flex items-center justify-between">
              <div class="text-center">
                <UAvatar :alt="selectedCombat.roliste1" size="lg" class="mb-2" />
                <div class="font-semibold">{{ selectedCombat.roliste1 }}</div>
                <div class="text-sm text-slate-600">{{ selectedCombat.personnage1 || 'Personnage 1' }}</div>
              </div>
              <div class="text-2xl font-bold text-red-500">VS</div>
              <div class="text-center">
                <UAvatar :alt="selectedCombat.roliste2" size="lg" class="mb-2" />
                <div class="font-semibold">{{ selectedCombat.roliste2 }}</div>
                <div class="text-sm text-slate-600">{{ selectedCombat.personnage2 || 'Personnage 2' }}</div>
              </div>
            </div>
          </div>

          <!-- Mise -->
          <div class="bg-emerald-50 rounded-lg p-4 text-center">
            <h4 class="font-semibold mb-2">Mise en jeu</h4>
            <div class="text-3xl font-bold text-emerald-600">{{ selectedCombat.valeur }} USD</div>
          </div>

          <!-- Actions -->
          <div class="flex gap-3 pt-4">
            <UButton
              v-if="selectedCombat.statut === 'En attente'"
              label="Participer au combat"
              color="emerald"
              class="flex-1"
              @click="participerCombat(selectedCombat.id)"
            />
            <UButton
              v-if="selectedCombat.statut === 'En cours' && isArbitre(selectedCombat)"
              label="Déclarer le vainqueur"
              color="primary"
              class="flex-1"
              @click="declarerVainqueur(selectedCombat.id)"
            />
            <UButton
              label="Voir l'arène"
              color="gray"
              variant="outline"
              @click="voirArene(selectedCombat.id)"
            />
          </div>
        </div>
      </div>
    </UModal>

    <!-- Bouton flottant pour créer un combat -->
    <div class="fixed bottom-8 right-8 z-50">
      <UButton
        icon="i-lucide-plus"
        size="xl"
        color="emerald"
        class="rounded-full shadow-2xl hover:shadow-emerald-500/25 transition-all duration-300 hover:scale-110"
        @click="openCreateCombatModal"
      />
    </div>

    <!-- Composant de création de combat -->
    <!-- <CreateCombat 
      v-model="showCreateModal" 
      @combat-created="handleCombatCreated"
    /> -->
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'users'
})

// États réactifs
const activeFilter = ref('tous')
const searchQuery = ref('')
const sortBy = ref('recent')
const showCombatModal = ref(false)
const showCreateModal = ref(false)
const selectedCombat = ref(null)

// Options de filtres
const filters = [
  { key: 'tous', label: 'Tous les combats' },
  { key: 'en-cours', label: 'En cours' },
  { key: 'en-attente', label: 'En attente' },
  { key: 'termines', label: 'Terminés' },
  { key: 'mes-combats', label: 'Mes combats' }
]

const sortOptions = [
  { value: 'recent', label: 'Plus récents' },
  { value: 'ancien', label: 'Plus anciens' },
  { value: 'mise-croissante', label: 'Mise croissante' },
  { value: 'mise-decroissante', label: 'Mise décroissante' }
]

// Données des combats (à remplacer par une API)
const mises = ref([
  {
    id: 'SHIBIRIK0001',
    roliste1: "Rik",
    personnage1: "Uchiha Tagar",
    roliste2: "Prince", 
    personnage2: "Senju Tiger-rama",
    groupe: 'Shinobi New Generation',
    valeur: 25,
    arbitre: 'Aristarque',
    statut: 'En cours',
    typeCombat: 'Singulier',
    dateCreation: new Date('2024-01-15'),
    lienArene: 'https://example.com/arene1'
  },
  {
    id: 'SHIBIRIK0002',
    roliste1: "Sanogo",
    personnage1: "Hyuga Shiro", 
    roliste2: "Rik",
    personnage2: "Uzumaki Harukai",
    groupe: 'Shinobi New Generation',
    valeur: 15,
    arbitre: 'Prince',
    statut: 'En attente',
    typeCombat: 'Singulier',
    dateCreation: new Date('2024-01-18'),
    lienArene: null
  },
  {
    id: 'SHIBIRIK0003',
    roliste1: "Prince",
    personnage1: "Yuki Grimray",
    roliste2: "Aristarque", 
    personnage2: "Aeshisen Shiraemon",
    groupe: 'Shinobi New Generation',
    valeur: 35,
    arbitre: 'Sanogo',
    statut: 'Terminé',
    typeCombat: 'Duo',
    dateCreation: new Date('2024-01-10'),
    lienArene: 'https://example.com/arene3',
    vainqueur: 'Prince'
  }
])

// Computed
const combatsStats = computed(() => ({
  total: mises.value.length,
  enCours: mises.value.filter(m => m.statut === 'En cours').length,
  termines: mises.value.filter(m => m.statut === 'Terminé').length,
  enAttente: mises.value.filter(m => m.statut === 'En attente').length
}))

const filteredMises = computed(() => {
  let filtered = [...mises.value]

  // Filtrage par statut
  if (activeFilter.value !== 'tous') {
    switch (activeFilter.value) {
      case 'en-cours':
        filtered = filtered.filter(m => m.statut === 'En cours')
        break
      case 'en-attente':
        filtered = filtered.filter(m => m.statut === 'En attente')
        break
      case 'termines':
        filtered = filtered.filter(m => m.statut === 'Terminé')
        break
      case 'mes-combats':
        // À implémenter avec l'utilisateur connecté
        filtered = filtered.filter(m => m.roliste1 === 'Rik' || m.roliste2 === 'Rik')
        break
    }
  }

  // Recherche
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    filtered = filtered.filter(m => 
      m.roliste1.toLowerCase().includes(query) ||
      m.roliste2.toLowerCase().includes(query) ||
      m.personnage1?.toLowerCase().includes(query) ||
      m.personnage2?.toLowerCase().includes(query) ||
      m.id.toLowerCase().includes(query)
    )
  }

  // Tri
  switch (sortBy.value) {
    case 'recent':
      filtered.sort((a, b) => new Date(b.dateCreation) - new Date(a.dateCreation))
      break
    case 'ancien':
      filtered.sort((a, b) => new Date(a.dateCreation) - new Date(b.dateCreation))
      break
    case 'mise-croissante':
      filtered.sort((a, b) => a.valeur - b.valeur)
      break
    case 'mise-decroissante':
      filtered.sort((a, b) => b.valeur - a.valeur)
      break
  }

  return filtered
})

// Méthodes
const setFilter = (filterKey) => {
  activeFilter.value = filterKey
}

const getStatusColor = (statut) => {
  switch (statut) {
    case 'En cours': return 'yellow'
    case 'En attente': return 'blue'
    case 'Terminé': return 'green'
    default: return 'gray'
  }
}

const formatDate = (date) => {
  return new Intl.DateTimeFormat('fr-FR', {
    day: 'numeric',
    month: 'short',
    year: 'numeric'
  }).format(date)
}

const openCombatModal = (combat) => {
  selectedCombat.value = combat
  showCombatModal.value = true
}

const openCreateCombatModal = () => {
  showCreateModal.value = true
}

const participerCombat = (combatId) => {
  // Logique pour participer à un combat
  console.log('Participation au combat:', combatId)
  // Ici on ajouterait la logique pour rejoindre le combat
}

const declarerVainqueur = (combatId) => {
  // Logique pour déclarer un vainqueur (arbitre uniquement)
  console.log('Déclaration du vainqueur pour:', combatId)
}

const voirArene = (combatId) => {
  const combat = mises.value.find(m => m.id === combatId)
  if (combat?.lienArene) {
    window.open(combat.lienArene, '_blank')
  }
}

const isArbitre = (combat) => {
  // À remplacer par la vérification de l'utilisateur connecté
  return combat.arbitre === 'currentUser'
}

const handleCombatCreated = (newCombat) => {
  mises.value.unshift(newCombat)
  showCreateModal.value = false
}
</script>