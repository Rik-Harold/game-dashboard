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
                :options="personnages"
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
                :options="personnages"
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
                :options="personnages"
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
                :options="personnages"
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
              <span class="text-emerald-800 font-bold">{{ formData.valeur }} USD</span>
            </div>
          </div>
          <p class="text-xs text-gray-500 mt-1">
            Montant minimum: 1 USD - Maximum: 100 USD
          </p>
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
            <div><strong>Mise:</strong> {{ formData.valeur }} USD</div>
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
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['update:modelValue', 'combat-created'])

// État du modal
const isOpen = computed({
  get: () => props.modelValue,
  set: (value) => emit('update:modelValue', value)
})
</script>