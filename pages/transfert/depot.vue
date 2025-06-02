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
          <NuxtLink to="/notifications" class="relative">
            <UIcon name="i-lucide-bell" class="text-xl text-gray-300 hover:text-white transition-colors" />
            <span v-if="notificationCount > 0" 
                  class="absolute -top-2 -right-2 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
              {{ notificationCount }}
            </span>
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
        <div class="text-center mb-8">
          <h1 class="text-4xl font-bold text-white mb-4">Recharger mon portefeuille</h1>
          <p class="text-gray-300">Ajoutez des fonds à votre portefeuille TipLink pour participer aux combats</p>
        </div>

        <!-- Wallet Balance -->
        <div class="bg-gradient-to-r from-emerald-900/20 to-blue-900/20 rounded-xl p-6 mb-8 border border-emerald-500/20">
          <div class="flex items-center justify-between">
            <div>
              <h3 class="text-lg font-semibold text-white mb-2">Solde actuel</h3>
              <div class="text-3xl font-bold text-emerald-400">
                {{ currentBalance.toFixed(6) }} TRON
              </div>
              <div class="text-gray-300 text-sm">
                ≈ {{ (currentBalance * tronToUsd).toFixed(2) }} USD
              </div>
            </div>
            <UIcon name="i-lucide-wallet" class="text-4xl text-emerald-400" />
          </div>
        </div>

        <!-- Deposit Form -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
          <!-- Form Section -->
          <div class="bg-white/5 rounded-xl p-6 backdrop-blur-sm border border-white/10">
            <h2 class="text-2xl font-bold text-white mb-6">Effectuer un dépôt</h2>
            
            <form @submit.prevent="handleDeposit" class="space-y-6">
              <!-- Payment Method -->
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-3">Moyen de paiement</label>
                <div class="grid grid-cols-2 gap-3">
                  <button
                    v-for="method in paymentMethods"
                    :key="method.id"
                    type="button"
                    @click="selectedPaymentMethod = method.id"
                    :class="[
                      'p-4 rounded-lg border-2 transition-all',
                      selectedPaymentMethod === method.id
                        ? 'border-emerald-500 bg-emerald-500/10 text-white'
                        : 'border-gray-600 bg-gray-700/20 text-gray-300 hover:border-gray-500'
                    ]"
                  >
                    <UIcon :name="method.icon" class="text-2xl mb-2" />
                    <div class="text-sm font-medium">{{ method.name }}</div>
                  </button>
                </div>
              </div>

              <!-- Amount -->
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-2">
                  Montant en TRON
                </label>
                <UInput
                  v-model="depositForm.amount"
                  type="number"
                  step="0.000001"
                  min="0"
                  placeholder="0.000000"
                  @input="calculateFees"
                  class="w-full"
                />
              </div>

              <!-- Currency Selection -->
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-2">
                  Devise de référence
                </label>
                <USelect
                  v-model="selectedCurrency"
                  :options="currencies"
                  option-attribute="name"
                  value-attribute="code"
                  @change="calculateFees"
                  class="w-full"
                />
              </div>

              <!-- Transaction Proof -->
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-2">
                  Justificatif de transaction
                </label>
                <UInput
                  type="file"
                  accept="image/*,.pdf"
                  @change="handleFileUpload"
                  class="w-full"
                />
                <p class="text-xs text-gray-400 mt-1">
                  Formats acceptés: JPG, PNG, PDF (max 5MB)
                </p>
              </div>

              <!-- Submit Button -->
              <UButton
                type="submit"
                color="emerald"
                size="lg"
                :loading="isLoading"
                :disabled="!canSubmit"
                class="w-full"
              >
                Envoyer la demande de dépôt
              </UButton>
            </form>
          </div>

          <!-- Summary Section -->
          <div class="bg-white/5 rounded-xl p-6 backdrop-blur-sm border border-white/10">
            <h3 class="text-xl font-bold text-white mb-6">Récapitulatif du dépôt</h3>
            
            <div class="space-y-4">
              <!-- Amount Summary -->
              <div class="flex justify-between items-center py-2 border-b border-gray-600">
                <span class="text-gray-300">Montant TRON</span>
                <span class="text-white font-medium">{{ depositForm.amount || '0' }} TRON</span>
              </div>
              
              <div class="flex justify-between items-center py-2 border-b border-gray-600">
                <span class="text-gray-300">Équivalent {{ selectedCurrency }}</span>
                <span class="text-white font-medium">
                  {{ fiatEquivalent.toFixed(2) }} {{ selectedCurrency }}
                </span>
              </div>

              <!-- Fees -->
              <div class="flex justify-between items-center py-2 border-b border-gray-600">
                <span class="text-gray-300">Frais de dépôt ({{ currentPaymentMethod?.fee }}%)</span>
                <span class="text-red-400 font-medium">
                  {{ depositFees.toFixed(2) }} {{ selectedCurrency }}
                </span>
              </div>

              <!-- Total -->
              <div class="flex justify-between items-center py-2 text-lg font-bold">
                <span class="text-white">Total à envoyer</span>
                <span class="text-emerald-400">
                  {{ totalAmount.toFixed(2) }} {{ selectedCurrency }}
                </span>
              </div>

              <!-- Payment Instructions -->
              <div v-if="currentPaymentMethod && depositForm.amount > 0" 
                   class="mt-6 p-4 bg-blue-900/20 rounded-lg border border-blue-500/20">
                <h4 class="text-sm font-semibold text-blue-300 mb-2">Instructions de paiement</h4>
                <div class="text-sm text-gray-300 space-y-1">
                  <p><span class="font-medium">Destinataire:</span> {{ currentPaymentMethod.recipient }}</p>
                  <p><span class="font-medium">Référence:</span> {{ generateReference() }}</p>
                  <p class="text-xs text-yellow-300 mt-2">
                    ⚠️ N'oubliez pas d'inclure la référence dans votre envoi
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </UContainer>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  // middleware: 'auth', // À implémenter
  layout: 'users'
})

// Types
interface PaymentMethod {
  id: string
  name: string
  icon: string
  fee: number // en pourcentage
  recipient: string
}

interface Currency {
  code: string
  name: string
  symbol: string
  rate: number // taux par rapport à USD
}

// Reactive data
const isLoading = ref(false)
const notificationCount = ref(3) // À connecter avec l'API
const currentBalance = ref(12.456789) // À connecter avec TipLink API

const depositForm = reactive({
  amount: '',
  paymentMethod: '',
  transactionProof: null as File | null
})

const selectedPaymentMethod = ref('')
const selectedCurrency = ref('EUR')

// Payment methods configuration
const paymentMethods: PaymentMethod[] = [
  {
    id: 'paypal',
    name: 'PayPal',
    icon: 'i-lucide-credit-card',
    fee: 3.5,
    recipient: 'payments@shinobirik.com'
  },
  {
    id: 'wero',
    name: 'Wero',
    icon: 'i-lucide-smartphone',
    fee: 2.0,
    recipient: '+33123456789'
  },
  {
    id: 'mtn',
    name: 'MTN Money',
    icon: 'i-lucide-phone',
    fee: 2.5,
    recipient: '+237123456789'
  },
  {
    id: 'western',
    name: 'Western Union',
    icon: 'i-lucide-building',
    fee: 4.0,
    recipient: 'ShinobiRik SARL'
  }
]

// Currencies configuration
const currencies: Currency[] = [
  { code: 'EUR', name: 'Euro', symbol: '€', rate: 0.85 },
  { code: 'USD', name: 'Dollar US', symbol: '$', rate: 1.0 },
  { code: 'XAF', name: 'Franc CFA', symbol: 'FCFA', rate: 600 },
  { code: 'CNY', name: 'Yuan', symbol: '¥', rate: 7.0 }
]

// Market data (À connecter avec CoinGecko/CryptoCompare)
const tronToUsd = ref(0.12) // Prix TRON en USD

// Computed
const currentPaymentMethod = computed(() => 
  paymentMethods.find(method => method.id === selectedPaymentMethod.value)
)

const currentCurrency = computed(() =>
  currencies.find(currency => currency.code === selectedCurrency.value) || currencies[0]
)

const fiatEquivalent = computed(() => {
  const amount = parseFloat(depositForm.amount) || 0
  return amount * tronToUsd.value * currentCurrency.value.rate
})

const depositFees = computed(() => {
  if (!currentPaymentMethod.value) return 0
  return fiatEquivalent.value * (currentPaymentMethod.value.fee / 100)
})

const totalAmount = computed(() => fiatEquivalent.value + depositFees.value)

const canSubmit = computed(() => {
  return depositForm.amount &&
         selectedPaymentMethod.value &&
         depositForm.transactionProof &&
         parseFloat(depositForm.amount) > 0
})

// Methods
function calculateFees() {
  // Déjà géré par les computed properties
}

function handleFileUpload(event: Event) {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  
  if (file) {
    // Validation de la taille (5MB max)
    if (file.size > 5 * 1024 * 1024) {
      alert('Le fichier ne peut pas dépasser 5MB')
      return
    }
    
    depositForm.transactionProof = file
  }
}

function generateReference(): string {
  const timestamp = Date.now().toString().slice(-6)
  const randomStr = Math.random().toString(36).substring(2, 6).toUpperCase()
  return `SBR-${timestamp}-${randomStr}`
}

async function handleDeposit() {
  if (!canSubmit.value) return
  
  isLoading.value = true
  
  try {
    // Créer FormData pour l'upload
    const formData = new FormData()
    formData.append('amount', depositForm.amount)
    formData.append('paymentMethod', selectedPaymentMethod.value)
    formData.append('currency', selectedCurrency.value)
    formData.append('reference', generateReference())
    if (depositForm.transactionProof) {
      formData.append('transactionProof', depositForm.transactionProof)
    }
    
    // Simuler l'appel API
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Rediriger vers les transactions ou afficher une confirmation
    alert('Demande de dépôt envoyée avec succès !')
    await navigateTo('/transactions')
    
  } catch (error) {
    console.error('Erreur lors du dépôt:', error)
    alert('Erreur lors de l\'envoi de la demande')
  } finally {
    isLoading.value = false
  }
}

async function logout() {
  // Implémenter la déconnexion
  await navigateTo('/')
}

// Lifecycle
onMounted(async () => {
  // Charger le solde du portefeuille
  // await loadWalletBalance()
  
  // Charger les taux de change
  // await loadExchangeRates()
})
</script>