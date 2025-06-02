<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900">
    <!-- Navigation Header -->
    <nav class="fixed top-0 w-full z-50 bg-black/20 backdrop-blur-lg border-b border-white/10">
      <UContainer class="flex items-center justify-between py-4">
        <div class="flex items-center space-x-2">
          <NuxtLink to="/" class="flex items-center space-x-2">
            <UIcon name="i-lucide-sword" class="text-2xl text-emerald-400" />
            <h1 class="text-xl font-bold text-white">ShinobiRik</h1>
          </NuxtLink>
        </div>
        
        <div class="flex items-center space-x-4">
          <NuxtLink to="/depot" class="text-gray-300 hover:text-white transition-colors">
            <UIcon name="i-lucide-wallet" class="text-xl" />
          </NuxtLink>
          <NuxtLink to="/profil" class="text-gray-300 hover:text-white transition-colors">
            <UIcon name="i-lucide-user" class="text-xl" />
          </NuxtLink>
          <UButton color="emerald" variant="outline" @click="logout">
            Déconnexion
          </UButton>
        </div>
      </UContainer>
    </nav>

    <!-- Main Content -->
    <div class="pt-24 pb-16 px-4">
      <UContainer class="max-w-4xl mx-auto">
        <!-- Header -->
        <div class="flex items-center justify-between mb-8">
          <div>
            <h1 class="text-4xl font-bold text-white mb-2">Notifications</h1>
            <p class="text-gray-300">
              {{ unreadCount }} notification{{ unreadCount > 1 ? 's' : '' }} non lue{{ unreadCount > 1 ? 's' : '' }}
            </p>
          </div>
          
          <div class="flex space-x-3">
            <UButton 
              color="gray" 
              variant="outline" 
              @click="markAllAsRead"
              :disabled="unreadCount === 0"
            >
              Tout marquer comme lu
            </UButton>
            <UButton 
              color="red" 
              variant="outline" 
              @click="deleteAllRead"
            >
              Supprimer les lues
            </UButton>
          </div>
        </div>

        <!-- Filters -->
        <div class="flex flex-wrap gap-3 mb-6">
          <UButton
            v-for="filter in notificationFilters"
            :key="filter.type"
            :color="selectedFilter === filter.type ? 'emerald' : 'gray'"
            :variant="selectedFilter === filter.type ? 'solid' : 'outline'"
            size="sm"
            @click="selectedFilter = filter.type"
          >
            <UIcon :name="filter.icon" class="mr-2" />
            {{ filter.label }}
            <span v-if="filter.count > 0" class="ml-2 bg-white/20 px-2 py-1 rounded-full text-xs">
              {{ filter.count }}
            </span>
          </UButton>
        </div>

        <!-- Notifications List -->
        <div class="space-y-4">
          <div
            v-for="notification in filteredNotifications"
            :key="notification.id"
            :class="[
              'bg-white/5 rounded-xl p-6 backdrop-blur-sm border transition-all cursor-pointer',
              notification.read 
                ? 'border-white/10 opacity-75' 
                : 'border-emerald-500/20 hover:border-emerald-500/40'
            ]"
            @click="handleNotificationClick(notification)"
          >
            <div class="flex items-start space-x-4">
              <!-- Icon -->
              <div :class="[
                'w-12 h-12 rounded-lg flex items-center justify-center',
                getNotificationStyle(notification.type).bg
              ]">
                <UIcon 
                  :name="getNotificationStyle(notification.type).icon" 
                  :class="['text-xl', getNotificationStyle(notification.type).color]"
                />
              </div>

              <!-- Content -->
              <div class="flex-1">
                <div class="flex items-start justify-between">
                  <div>
                    <h3 :class="[
                      'text-lg font-semibold mb-1',
                      notification.read ? 'text-gray-300' : 'text-white'
                    ]">
                      {{ notification.title }}
                    </h3>
                    <p :class="[
                      'text-sm mb-3',
                      notification.read ? 'text-gray-400' : 'text-gray-300'
                    ]">
                      {{ notification.message }}
                    </p>
                  </div>
                  
                  <!-- Actions -->
                  <div class="flex items-center space-x-2">
                    <span class="text-xs text-gray-400">
                      {{ formatTime(notification.createdAt) }}
                    </span>
                    <div class="flex space-x-1">
                      <UButton
                        v-if="!notification.read"
                        color="gray"
                        variant="ghost"
                        size="xs"
                        @click.stop="markAsRead(notification.id)"
                      >
                        <UIcon name="i-lucide-check" />
                      </UButton>
                      <UButton
                        color="red"
                        variant="ghost"
                        size="xs"
                        @click.stop="deleteNotification(notification.id)"
                      >
                        <UIcon name="i-lucide-trash-2" />
                      </UButton>
                    </div>
                  </div>
                </div>

                <!-- Action Buttons for specific notifications -->
                <div v-if="notification.actions && notification.actions.length > 0" 
                     class="flex space-x-3 mt-4">
                  <UButton
                    v-for="action in notification.actions"
                    :key="action.type"
                    :color="action.color"
                    size="sm"
                    @click.stop="handleAction(notification, action)"
                  >
                    {{ action.label }}
                  </UButton>
                </div>
              </div>
            </div>
          </div>

          <!-- Empty State -->
          <div v-if="filteredNotifications.length === 0" 
               class="text-center py-12 bg-white/5 rounded-xl backdrop-blur-sm border border-white/10">
            <UIcon name="i-lucide-bell-off" class="text-6xl text-gray-400 mb-4" />
            <h3 class="text-xl font-semibold text-gray-300 mb-2">Aucune notification</h3>
            <p class="text-gray-400">
              {{ selectedFilter === 'all' ? 'Vous n\'avez aucune notification.' : 'Aucune notification de ce type.' }}
            </p>
          </div>
        </div>

        <!-- Load More -->
        <div v-if="hasMore" class="text-center mt-8">
          <UButton 
            color="gray" 
            variant="outline"
            :loading="loadingMore"
            @click="loadMoreNotifications"
          >
            Charger plus
          </UButton>
        </div>
      </UContainer>
    </div>

    <!-- Modals -->
    <!-- Combat Details Modal -->
    <UModal v-model="combatDetailsModal.open" :ui="{ width: 'sm:max-w-2xl' }">
      <template #header>
        <h3 class="text-lg font-semibold text-white">Détails du combat</h3>
      </template>
      
      <div v-if="combatDetailsModal.combat" class="p-6 space-y-4">
        <div class="grid grid-cols-2 gap-4">
          <div>
            <h4 class="text-sm font-medium text-gray-300 mb-1">Challenger</h4>
            <p class="text-white">{{ combatDetailsModal.combat.challenger }}</p>
          </div>
          <div>
            <h4 class="text-sm font-medium text-gray-300 mb-1">Défenseur</h4>
            <p class="text-white">{{ combatDetailsModal.combat.defender }}</p>
          </div>
        </div>
        
        <div>
          <h4 class="text-sm font-medium text-gray-300 mb-1">Mise</h4>
          <p class="text-emerald-400 font-semibold">{{ combatDetailsModal.combat.stake }} TRON</p>
        </div>
        
        <div>
          <h4 class="text-sm font-medium text-gray-300 mb-1">Règles</h4>
          <p class="text-gray-300">{{ combatDetailsModal.combat.rules }}</p>
        </div>
      </div>
      
      <template #footer>
        <div class="flex justify-end space-x-3">
          <UButton color="gray" variant="outline" @click="combatDetailsModal.open = false">
            Fermer
          </UButton>
          <UButton v-if="combatDetailsModal.combat?.needsAction" color="emerald">
            Accepter l'arbitrage
          </UButton>
        </div>
      </template>
    </UModal>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  middleware: 'auth',
  layout: 'default'
})

// Types
interface NotificationAction {
  type: string
  label: string
  color: string
}

interface Notification {
  id: string
  type: 'challenge' | 'arbitrage' | 'result' | 'deposit' | 'withdrawal' | 'system'
  title: string
  message: string
  read: boolean
  createdAt: string
  data?: any
  actions?: NotificationAction[]
}

interface CombatDetails {
  challenger: string
  defender: string
  stake: number
  rules: string
  needsAction?: boolean
}

// Reactive data
const selectedFilter = ref('all')
const loadingMore = ref(false)
const hasMore = ref(true)

const combatDetailsModal = reactive({
  open: false,
  combat: null as CombatDetails | null
})

// Sample notifications data (À remplacer par l'API)
const notifications = ref<Notification[]>([
  {
    id: '1',
    type: 'arbitrage',
    title: 'Demande d\'arbitrage',
    message: 'Vous avez été sélectionné comme arbitre pour un combat entre NarutoFan et SasukeKing',
    read: false,
    createdAt: '2025-06-02T10:30:00Z',
    data: {
      combatId: 'combat-123',
      challenger: 'NarutoFan',
      defender: 'SasukeKing',
      stake: 50,
      rules: 'Combat singulier, 3 rounds maximum, techniques de base seulement'
    },
    actions: [
      { type: 'accept_arbitrage', label: 'Accepter', color: 'emerald' },
      { type: 'decline_arbitrage', label: 'Refuser', color: 'red' }
    ]
  },
  {
    id: '2',
    type: 'challenge',
    title: 'Nouveau défi reçu',
    message: 'SakuraCherry vous défie en combat avec une mise de 25 TRON',
    read: false,
    createdAt: '2025-06-02T09:15:00Z',
    actions: [
      { type: 'accept_challenge', label: 'Accepter', color: 'emerald' },
      { type: 'decline_challenge', label: 'Refuser', color: 'red' }
    ]
  },
  {
    id: '3',
    type: 'result',
    title: 'Résultat de combat',
    message: 'Victoire ! Vous avez gagné 75 TRON contre KakashiSensei',
    read: true,
    createdAt: '2025-06-01T16:45:00Z'
  },
  {
    id: '4',
    type: 'deposit',
    title: 'Dépôt confirmé',
    message: 'Votre dépôt de 100 TRON a été crédité sur votre portefeuille',
    read: false,
    createdAt: '2025-06-01T14:20:00Z'
  },
  {
    id: '5',
    type: 'system',
    title: 'Maintenance programmée',
    message: 'Une maintenance est programmée le 5 juin de 2h à 4h du matin',
    read: true,
    createdAt: '2025-05-30T12:00:00Z'
  }
])

// Filters configuration
const notificationFilters = computed(() => [
  { 
    type: 'all', 
    label: 'Toutes', 
    icon: 'i-lucide-inbox',
    count: notifications.value.length 
  },
  { 
    type: 'challenge', 
    label: 'Défis', 
    icon: 'i-lucide-swords',
    count: notifications.value.filter(n => n.type === 'challenge').length 
  },
  { 
    type: 'arbitrage', 
    label: 'Arbitrages', 
    icon: 'i-lucide-gavel',
    count: notifications.value.filter(n => n.type === 'arbitrage').length 
  },
  { 
    type: 'result', 
    label: 'Résultats', 
    icon: 'i-lucide-trophy',
    count: notifications.value.filter(n => n.type ===