<template>
  <UModal v-model="isOpen" :ui="{ width: 'sm:max-w-3xl' }">
    <div class="p-6">
      <div class="flex items-center justify-between mb-6">
        <h3 class="text-2xl font-bold">Créer un Défi de Combat</h3>
        <UButton
          icon="i-lucide-x"
          color="gray"
          variant="ghost"
          size="sm"
          @click="closeModal"
        />
      </div>

      <form @submit.prevent="createCombat" class="space-y-6">
        <!-- Type de combat -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Type de combat</label>
          <div class="grid grid-cols-3 gap-3">
            <UButton
              v-for="type in typeCombats"
              :key="type.key"
              :label="type.label"
              :color="formData.typeCombat === type.key ? 'primary' : 'gray'"
              :variant="formData.typeCombat === type.key ? 'solid' : 'outline'"
              class="w-full"
              @click="formData.typeCombat = type.key"
              type="button"
            />
          </div>
        </div>

        <!-- Adversaires -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Combattant 1 -->
          <div class="bg-blue-50 rounded-lg p-4">
            <h4 class="font-semibold text-blue-800 mb-3">Combattant 1</h4>
            <div class="space-y-3">
              <USelectMenu
                v-model="formData.roliste1"
                :options="rolistes"
                placeholder="Sélectionner le rôliste"
                option-attribute="label"
                value-attribute="id"
              />
              <USelectMenu
                v-model="formData.personnage1"
                :options="getPersonnagesForRoliste(formData.roliste1)"
                placeholder="Sélectionner le personnage"
                option-attribute="label"
                value-attribute="id"
                :disabled="!formData.roliste1"
              />
            </div>
          </div>

          <!-- Combattant 2 -->
          <div class="bg-red-50 rounded-lg p-4">
            <h4 class="font-semibold text-red-800 mb-3">Combattant 2</h4>
            <div class="space-y-3">
              <USelectMenu
                v-model="formData.roliste2"
                :options="rolistesDisponibles2"
                placeholder="Sélectionner le rôliste"
                option-attribute="label"
                value-attribute="id"
              />
              <USelectMenu
                v-model="formData.personnage2"
                :options="getPersonnagesForRoliste(formData.roliste2)"
                placeholder="Sélectionner le personnage"
                option-attribute="label"
                value-attribute="id"
                :disabled="!formData.roliste2"
              />
            </div>
          </div>
        </div>

        <!-- Combattants additionnels pour les combats en duo -->
        <div v-if="formData.typeCombat === 'duo' || formData.typeCombat === '1vs2'" class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Équipier 1 -->
          <div v-if="formData.typeCombat === 'duo'" class="bg-blue-50 rounded-lg p-4">
            <h4 class="font-semibold text-blue-800 mb-3">Équipier 1</h4>
            <div class="space-y-3">
              <USelectMenu
                v-model="formData.equipier1"
                :options="rolistesDisponibles3"
                placeholder="Sélectionner l'équipier"
                option-attribute="label"
                value-attribute="id"
              />
              <USelectMenu
                v-model="formData.personnageEquipier1"
                :options="getPersonnagesForRoliste(formData.equipier1)"
                placeholder="Sélectionner le personnage"
                option-attribute="label"
                value-attribute="id"
                :disabled="!formData.equipier1"
              />
            </div>
          </div>

          <!-- Équipier 2 -->
          <div class="bg-red-50 rounded-lg p-4">
            <h4 class="font-semibold text-red-800 mb-3">
              {{ formData.typeCombat === 'duo' ? 'Équipier 2' : 'Équipier adverse' }}
            </h4>
            <div class="space-y-3">
              <USelectMenu
                v-model="formData.equipier2"
                :options="rolistesDisponibles4"
                placeholder="Sélectionner l'équipier"
                option-attribute="label"
                value-attribute="id"
              />
              <USelectMenu
                v-model="formData.personnageEquipier2"
                :options="getPersonnagesForRoliste(formData.equipier2)"
                placeholder="Sélectionner le personnage"
                option-attribute="label"
                value-attribute="id"
                :disabled="!formData.equipier2"
              />
            </div>
          </div>
        </div>

        <!-- Arbitre -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Arbitre proposé</label>
          <USelectMenu
            v-model="formData.arbitre"
            :options="arbitresDisponibles"
            placeholder="Sélectionner l'arbitre"
            option-attribute="label"
            value-attribute="id"
          />
          <p class="text-xs text-gray-500 mt-1">
            L'arbitre recevra une notification pour confirmer sa participation
          </p>
        </div>

        <!-- Lien de l'arène -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Lien de l'arène (optionnel)</label>
          <UInput
            v-model="formData.lienArene"
            placeholder="https://discord.gg/exemple ou lien vers l'arène"
            type="url"
          />
        </div>

        <!-- Mise -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Mise du combat</label>
          <div class="flex items-center gap-4">
            <URange
              v-model="formData.valeur"
              :min="1"
              :max="100"
              :step="1"
              class="flex-1"
            />
            <div class="bg-emerald-100 px-4 py-2 rounded-lg">
              <span class="text-emerald-800 font-bold">{{ formData.valeur }} USDC</span>
            </div>
          </div>
          <p class="text-xs text-gray-500 mt-1">
            Montant minimum: 1 USDC - Maximum: 100 USDC
          </p>
          <div class="mt-2 p-3 bg-yellow-50 rounded-lg">
            <p class="text-sm text-yellow-800">
              <UIcon name="i-lucide-info" class="inline mr-1" />
              Frais de service: {{ serviceFeesPercent }}% | Frais d'arbitrage: {{ arbitrageFeesPercent }}%
            </p>
          </div>
        </div>

        <!-- Description (optionnelle) -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Description du combat (optionnelle)</label>
          <UTextarea
            v-model="formData.description"
            placeholder="Ajoutez des détails sur le combat, les règles spéciales, etc."
            :rows="3"
          />
        </div>

        <!-- Récapitulatif -->
        <div class="bg-gray-50 rounded-lg p-4">
          <h4 class="font-semibold mb-3">Récapitulatif du combat</h4>
          <div class="space-y-2 text-sm">
            <div><strong>Type:</strong> {{ getTypeCombatLabel(formData.typeCombat) }}</div>
            <div v-if="formData.roliste1"><strong>Équipe 1:</strong> {{ getRolisteLabel(formData.roliste1) }}{{ formData.equipier1 ? ` & ${getRolisteLabel(formData.equipier1)}` : '' }}</div>
            <div v-if="formData.roliste2"><strong>Équipe 2:</strong> {{ getRolisteLabel(formData.roliste2) }}{{ formData.equipier2 ? ` & ${getRolisteLabel(formData.equipier2)}` : '' }}</div>
            <div v-if="formData.arbitre"><strong>Arbitre:</strong> {{ getRolisteLabel(formData.arbitre) }}</div>
            <div><strong>Mise totale:</strong> {{ formData.valeur * getNombreCombattants() }} USDC</div>
            <div class="text-xs text-gray-600">
              <div>Frais de service: {{ ((formData.valeur * getNombreCombattants()) * (serviceFeesPercent / 100)).toFixed(2) }} USDC</div>
              <div>Frais d'arbitrage: {{ ((formData.valeur * getNombreCombattants()) * (arbitrageFeesPercent / 100)).toFixed(2) }} USDC</div>
              <div class="font-semibold">Gain du vainqueur: {{ getVainqueurGain().toFixed(2) }} USDC</div>
            </div>
          </div>
        </div>

        <!-- Actions -->
        <div class="flex gap-3 pt-4">
          <UButton
            type="button"
            color="gray"
            variant="outline"
            class="flex-1"
            @click="closeModal"
          >
            Annuler
          </UButton>
          <UButton
            type="submit"
            color="primary"
            class="flex-1"
            :loading="isCreating"
            :disabled="!isFormValid"
          >
            Créer le défi
          </UButton>
        </div>
      </form>
    </div>
  </UModal>
</template>

<script setup>
import { z } from 'zod'

const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['update:modelValue', 'combat-created'])

// Configuration des frais
const serviceFeesPercent = 5 // 5% frais de service
const arbitrageFeesPercent = 10 // 10% frais d'arbitrage

// Schéma de validation Zod
const combatSchema = z.object({
  typeCombat: z.enum(['singulier', 'duo', '1vs2']),
  roliste1: z.string().min(1, 'Sélectionnez le premier combattant'),
  personnage1: z.string().min(1, 'Sélectionnez le personnage du premier combattant'),
  roliste2: z.string().min(1, 'Sélectionnez le second combattant'),
  personnage2: z.string().min(1, 'Sélectionnez le personnage du second combattant'),
  equipier1: z.string().optional(),
  personnageEquipier1: z.string().optional(),
  equipier2: z.string().optional(),
  personnageEquipier2: z.string().optional(),
  arbitre: z.string().min(1, 'Sélectionnez un arbitre'),
  valeur: z.number().min(1).max(100),
  lienArene: z.string().url().optional().or(z.literal('')),
  description: z.string().optional()
}).refine((data) => {
  // Validation conditionnelle pour les équipiers
  if (data.typeCombat === 'duo') {
    return data.equipier1 && data.personnageEquipier1 && data.equipier2 && data.personnageEquipier2
  }
  if (data.typeCombat === '1vs2') {
    return data.equipier2 && data.personnageEquipier2
  }
  return true
}, {
  message: "Tous les équipiers requis doivent être sélectionnés"
})

// État du modal
const isOpen = computed({
  get: () => props.modelValue,
  set: (value) => emit('update:modelValue', value)
})

// États réactifs
const isCreating = ref(false)
const formData = reactive({
  typeCombat: 'singulier',
  roliste1: '',
  personnage1: '',
  roliste2: '',
  personnage2: '',
  equipier1: '',
  personnageEquipier1: '',
  equipier2: '',
  personnageEquipier2: '',
  arbitre: '',
  valeur: 5,
  lienArene: '',
  description: ''
})

// Données statiques (à remplacer par des appels API)
const rolistes = ref([
  { label: 'Rik', id: 'rik' },
  { label: 'Prince', id: 'prince' },
  { label: 'Aristarque', id: 'aristarque' },
  { label: 'Sanogo', id: 'sanogo' }
])

const personnagesParRoliste = {
  'rik': [
    { label: 'Uchiha Tagar', id: 'tagar' },
    { label: 'Uzumaki Harukai', id: 'harukai' }
  ],
  'prince': [
    { label: 'Senju Tiger-rama', id: 'tiger-rama' },
    { label: 'Yuki Grimray', id: 'grimray' }
  ],
  'aristarque': [
    { label: 'Aeshisen Shiraemon', id: 'shiraemon' }
  ],
  'sanogo': [
    { label: 'Hyuga Shiro', id: 'shiro' },
    { label: 'Yossin', id: 'yossin' }
  ]
}

const typeCombats = [
  { key: 'singulier', label: 'Singulier' },
  { key: 'duo', label: 'Duo vs Duo' },
  { key: '1vs2', label: '1 vs Duo' }
]

// Computed properties
const rolistesDisponibles2 = computed(() => 
  rolistes.value.filter(r => r.id !== formData.roliste1)
)

const rolistesDisponibles3 = computed(() => 
  rolistes.value.filter(r => r.id !== formData.roliste1 && r.id !== formData.roliste2)
)

const rolistesDisponibles4 = computed(() => 
  rolistes.value.filter(r => 
    r.id !== formData.roliste1 && 
    r.id !== formData.roliste2 && 
    r.id !== formData.equipier1
  )
)

const arbitresDisponibles = computed(() => 
  rolistes.value.filter(r => 
    r.id !== formData.roliste1 && 
    r.id !== formData.roliste2 && 
    r.id !== formData.equipier1 && 
    r.id !== formData.equipier2
  )
)

const isFormValid = computed(() => {
  try {
    combatSchema.parse(formData)
    return true
  } catch {
    return false
  }
})

// Méthodes
const getPersonnagesForRoliste = (rolisteId) => {
  return personnagesParRoliste[rolisteId] || []
}

const getRolisteLabel = (rolisteId) => {
  return rolistes.value.find(r => r.id === rolisteId)?.label || ''
}

const getTypeCombatLabel = (type) => {
  return typeCombats.find(t => t.key === type)?.label || ''
}

const getNombreCombattants = () => {
  switch (formData.typeCombat) {
    case 'singulier': return 2
    case 'duo': return 4
    case '1vs2': return 3
    default: return 2
  }
}

const getVainqueurGain = () => {
  const miseTotal = formData.valeur * getNombreCombattants()
  const fraisService = miseTotal * (serviceFeesPercent / 100)
  const fraisArbitrage = miseTotal * (arbitrageFeesPercent / 100)
  return miseTotal - fraisService - fraisArbitrage
}

const closeModal = () => {
  isOpen.value = false
  resetForm()
}

const resetForm = () => {
  Object.assign(formData, {
    typeCombat: 'singulier',
    roliste1: '',
    personnage1: '',
    roliste2: '',
    personnage2: '',
    equipier1: '',
    personnageEquipier1: '',
    equipier2: '',
    personnageEquipier2: '',
    arbitre: '',
    valeur: 5,
    lienArene: '',
    description: ''
  })
}

const createCombat = async () => {
  try {
    isCreating.value = true
    
    // Validation avec Zod
    const validatedData = combatSchema.parse(formData)
    
    // Simulation d'appel API
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Génération de l'ID de combat
    const combatId = `SHIBIRIK${String(Date.now()).slice(-4).padStart(4, '0')}`
    
    const newCombat = {
      id: combatId,
      ...validatedData,
      statut: 'En attente',
      dateCreation: new Date(),
      groupe: 'Shinobi New Generation',
      miseTotal: formData.valeur * getNombreCombattants(),
      fraisService: (formData.valeur * getNombreCombattants()) * (serviceFeesPercent / 100),
      fraisArbitrage: (formData.valeur * getNombreCombattants()) * (arbitrageFeesPercent / 100),
      gainVainqueur: getVainqueurGain()
    }
    
    // Émettre l'événement de création
    emit('combat-created', newCombat)
    
    // Notification de succès
    const toast = useToast()
    toast.add({
      title: 'Combat créé !',
      description: `Le défi ${combatId} a été créé avec succès. L'arbitre va recevoir une notification.`,
      color: 'green'
    })
    
    closeModal()
    
  } catch (error) {
    console.error('Erreur lors de la création du combat:', error)
    
    const toast = useToast()
    toast.add({
      title: 'Erreur',
      description: 'Une erreur est survenue lors de la création du combat.',
      color: 'red'
    })
  } finally {
    isCreating.value = false
  }
}

// Watchers pour réinitialiser les personnages quand on change de rôliste
watch(() => formData.roliste1, () => {
  formData.personnage1 = ''
})

watch(() => formData.roliste2, () => {
  formData.personnage2 = ''
})

watch(() => formData.equipier1, () => {
  formData.personnageEquipier1 = ''
})

watch(() => formData.equipier2, () => {
  formData.personnageEquipier2 = ''
})

// Réinitialiser les équipiers quand on change de type de combat
watch(() => formData.typeCombat, () => {
  formData.equipier1 = ''
  formData.personnageEquipier1 = ''
  formData.equipier2 = ''
  formData.personnageEquipier2 = ''
})
</script>