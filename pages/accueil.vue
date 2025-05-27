<!-- pages/accueil.vue -->
<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900">
    <!-- Navigation Header -->
    <nav class="fixed top-0 w-full z-50 bg-black/20 backdrop-blur-lg border-b border-white/10">
      <UContainer class="flex items-center justify-between py-4">
        <!-- Logo -->
        <div class="flex items-center space-x-2">
          <UIcon name="i-lucide-sword" class="text-2xl text-emerald-400" />
          <h1 class="text-xl font-bold text-white">ShinobiRik</h1>
        </div>

        <!-- Navigation Menu -->
        <div class="hidden md:flex items-center space-x-6">
          <NuxtLink to="#features" class="text-gray-300 hover:text-white transition-colors">
            Fonctionnalités
          </NuxtLink>
          <NuxtLink to="#how-it-works" class="text-gray-300 hover:text-white transition-colors">
            Comment ça marche
          </NuxtLink>
          <NuxtLink to="#pricing" class="text-gray-300 hover:text-white transition-colors">
            Tarifs
          </NuxtLink>
        </div>

        <!-- Bouton de Connexion -->
        <UButton 
          @click="isLoginModalOpen = true"
          color="emerald" 
          variant="solid"
          class="bg-emerald-600 hover:bg-emerald-700 transition-all transform hover:scale-105"
        >
          Se connecter
        </UButton>
      </UContainer>
    </nav>

    <!-- Hero Section -->
    <section class="pt-24 pb-16 px-4">
      <UContainer class="text-center max-w-4xl mx-auto">
        <div class="animate-fade-in-up">
          <h1 class="text-5xl md:text-7xl font-bold text-white mb-6 leading-tight">
            Monétisez vos 
            <span class="text-transparent bg-clip-text bg-gradient-to-r from-emerald-400 to-blue-400">
              Combats Ninja
            </span>
          </h1>
          
          <p class="text-xl text-gray-300 mb-8 max-w-2xl mx-auto">
            Transformez vos affrontements de jeux de rôle en véritables défis monétisés. 
            Pariez, combattez et gagnez dans l'univers de Naruto.
          </p>

          <div class="flex flex-col sm:flex-row gap-4 justify-center mb-12">
            <UButton 
              @click="isLoginModalOpen = true"
              size="xl" 
              color="emerald"
              class="bg-emerald-600 hover:bg-emerald-700 transform hover:scale-105 transition-all px-8 py-4"
            >
              <UIcon name="i-lucide-play" class="mr-2" />
              Commencer maintenant
            </UButton>
            
            <UButton 
              size="xl" 
              color="white" 
              variant="outline"
              class="border-white/20 text-white hover:bg-white/10 transform hover:scale-105 transition-all px-8 py-4"
            >
              <UIcon name="i-lucide-info" class="mr-2" />
              En savoir plus
            </UButton>
          </div>
        </div>

        <!-- Stats -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8 mt-16">
          <div class="text-center p-6 bg-white/5 rounded-xl backdrop-blur-sm border border-white/10">
            <div class="text-3xl font-bold text-emerald-400 mb-2">500+</div>
            <div class="text-gray-300">Combats organisés</div>
          </div>
          <div class="text-center p-6 bg-white/5 rounded-xl backdrop-blur-sm border border-white/10">
            <div class="text-3xl font-bold text-blue-400 mb-2">10k$</div>
            <div class="text-gray-300">Gains distribués</div>
          </div>
          <div class="text-center p-6 bg-white/5 rounded-xl backdrop-blur-sm border border-white/10">
            <div class="text-3xl font-bold text-purple-400 mb-2">150+</div>
            <div class="text-gray-300">Ninjas actifs</div>
          </div>
        </div>
      </UContainer>
    </section>

    <!-- Features Section -->
    <section id="features" class="py-20 px-4">
      <UContainer class="max-w-6xl mx-auto">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-white mb-4">
            Fonctionnalités principales
          </h2>
          <p class="text-xl text-gray-300">
            Tout ce dont vous avez besoin pour organiser des combats épiques
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          <!-- Feature 1 -->
          <div class="p-6 bg-gradient-to-br from-emerald-900/20 to-emerald-800/20 rounded-xl border border-emerald-500/20 hover:border-emerald-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-emerald-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-swords" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Défis de Combat</h3>
            <p class="text-gray-300">Créez et participez à des défis de combat singulier ou en équipe avec des mises personnalisées.</p>
          </div>

          <!-- Feature 2 -->
          <div class="p-6 bg-gradient-to-br from-blue-900/20 to-blue-800/20 rounded-xl border border-blue-500/20 hover:border-blue-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-blue-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-wallet" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Portefeuille Intégré</h3>
            <p class="text-gray-300">Gérez vos gains avec TipLink. Dépôts et retraits sécurisés en crypto et fiat.</p>
          </div>

          <!-- Feature 3 -->
          <div class="p-6 bg-gradient-to-br from-purple-900/20 to-purple-800/20 rounded-xl border border-purple-500/20 hover:border-purple-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-purple-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-gavel" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Arbitrage Fair-Play</h3>
            <p class="text-gray-300">Système d'arbitrage communautaire pour garantir des combats équitables.</p>
          </div>

          <!-- Feature 4 -->
          <div class="p-6 bg-gradient-to-br from-orange-900/20 to-orange-800/20 rounded-xl border border-orange-500/20 hover:border-orange-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-orange-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-bell" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Notifications Temps Réel</h3>
            <p class="text-gray-300">Recevez des notifications pour les défis, résultats et opportunités d'arbitrage.</p>
          </div>

          <!-- Feature 5 -->
          <div class="p-6 bg-gradient-to-br from-pink-900/20 to-pink-800/20 rounded-xl border border-pink-500/20 hover:border-pink-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-pink-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-trophy" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Classements</h3>
            <p class="text-gray-300">Suivez vos performances et grimpez dans les classements de ninjas.</p>
          </div>

          <!-- Feature 6 -->
          <div class="p-6 bg-gradient-to-br from-teal-900/20 to-teal-800/20 rounded-xl border border-teal-500/20 hover:border-teal-500/40 transition-all transform hover:scale-105">
            <div class="w-12 h-12 bg-teal-600 rounded-lg flex items-center justify-center mb-4">
              <UIcon name="i-lucide-shield-check" class="text-white text-xl" />
            </div>
            <h3 class="text-xl font-semibold text-white mb-2">Sécurité Blockchain</h3>
            <p class="text-gray-300">Transactions sécurisées et transparentes grâce à la technologie blockchain.</p>
          </div>
        </div>
      </UContainer>
    </section>

    <!-- How it works Section -->
    <section id="how-it-works" class="py-20 px-4 bg-black/20">
      <UContainer class="max-w-4xl mx-auto text-center">
        <h2 class="text-4xl font-bold text-white mb-16">Comment ça marche</h2>
        
        <div class="space-y-12">
          <div class="flex flex-col md:flex-row items-center gap-8">
            <div class="w-16 h-16 bg-emerald-600 rounded-full flex items-center justify-center text-white text-2xl font-bold">1</div>
            <div class="flex-1 text-left">
              <h3 class="text-2xl font-semibold text-white mb-2">Créez votre défi</h3>
              <p class="text-gray-300">Choisissez vos adversaires, définissez les règles et la mise de votre combat ninja.</p>
            </div>
          </div>

          <div class="flex flex-col md:flex-row items-center gap-8">
            <div class="w-16 h-16 bg-blue-600 rounded-full flex items-center justify-center text-white text-2xl font-bold">2</div>
            <div class="flex-1 text-left">
              <h3 class="text-2xl font-semibold text-white mb-2">Trouvez un arbitre</h3>
              <p class="text-gray-300">Un arbitre de la communauté valide les règles et supervise le combat.</p>
            </div>
          </div>

          <div class="flex flex-col md:flex-row items-center gap-8">
            <div class="w-16 h-16 bg-purple-600 rounded-full flex items-center justify-center text-white text-2xl font-bold">3</div>
            <div class="flex-1 text-left">
              <h3 class="text-2xl font-semibold text-white mb-2">Combattez et gagnez</h3>
              <p class="text-gray-300">Menez votre combat RP et récupérez vos gains automatiquement.</p>
            </div>
          </div>
        </div>
      </UContainer>
    </section>

    <!-- CTA Section -->
    <section class="py-20 px-4">
      <UContainer class="text-center max-w-2xl mx-auto">
        <h2 class="text-4xl font-bold text-white mb-6">
          Prêt à rejoindre l'arène ?
        </h2>
        <p class="text-xl text-gray-300 mb-8">
          Créez votre compte et commencez à monétiser vos talents de ninja dès aujourd'hui.
        </p>
        <UButton 
          @click="isLoginModalOpen = true"
          size="xl" 
          color="emerald"
          class="bg-emerald-600 hover:bg-emerald-700 transform hover:scale-105 transition-all px-8 py-4"
        >
          Rejoindre maintenant
        </UButton>
      </UContainer>
    </section>

    <!-- Modal de Connexion/Inscription -->
    <UModal v-model="isLoginModalOpen" class="modal-custom">
      <div class="p-6 bg-gradient-to-br from-slate-800 to-slate-900 rounded-lg">
        <!-- Header -->
        <div class="flex items-center justify-between mb-6">
          <h2 class="text-2xl font-bold text-white">
            {{ isLoginMode ? 'Connexion' : 'Inscription' }}
          </h2>
          <UButton 
            @click="isLoginModalOpen = false"
            color="gray" 
            variant="ghost" 
            icon="i-lucide-x"
          />
        </div>

        <!-- Tabs -->
        <div class="flex mb-6 bg-slate-700/50 rounded-lg p-1">
          <button 
            @click="isLoginMode = true"
            :class="[
              'flex-1 py-2 px-4 rounded-md transition-all',
              isLoginMode ? 'bg-emerald-600 text-white' : 'text-gray-300 hover:text-white'
            ]"
          >
            Connexion
          </button>
          <button 
            @click="isLoginMode = false"
            :class="[
              'flex-1 py-2 px-4 rounded-md transition-all',
              !isLoginMode ? 'bg-emerald-600 text-white' : 'text-gray-300 hover:text-white'
            ]"
          >
            Inscription
          </button>
        </div>

        <!-- Formulaire de Connexion -->
        <div v-if="isLoginMode" class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Email</label>
            <UInput 
              v-model="loginForm.email" 
              type="email"
              placeholder="votre@email.com"
              class="w-full"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Mot de passe</label>
            <UInput 
              v-model="loginForm.password" 
              type="password"
              placeholder="••••••••"
              class="w-full"
            />
          </div>
          <UButton 
            @click="handleLogin"
            color="emerald" 
            class="w-full mt-6"
            :loading="isLoading"
          >
            Se connecter
          </UButton>
        </div>

        <!-- Formulaire d'Inscription -->
        <div v-else class="space-y-4">
          <div class="grid grid-cols-2 gap-4">
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-2">Nom</label>
              <UInput 
                v-model="registerForm.nom" 
                placeholder="Nom"
                class="w-full"
              />
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-2">Prénom</label>
              <UInput 
                v-model="registerForm.prenom" 
                placeholder="Prénom"
                class="w-full"
              />
            </div>
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Pseudo</label>
            <UInput 
              v-model="registerForm.pseudo" 
              placeholder="VotreNinja"
              class="w-full"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Email</label>
            <UInput 
              v-model="registerForm.email" 
              type="email"
              placeholder="votre@email.com"
              class="w-full"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Mot de passe</label>
            <UInput 
              v-model="registerForm.password" 
              type="password"
              placeholder="••••••••"
              class="w-full"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">Confirmer le mot de passe</label>
            <UInput 
              v-model="registerForm.confirmPassword" 
              type="password"
              placeholder="••••••••"
              class="w-full"
            />
          </div>
          
          <UButton 
            @click="handleRegister"
            color="emerald" 
            class="w-full mt-6"
            :loading="isLoading"
          >
            S'inscrire
          </UButton>
        </div>

        <!-- Divider -->
        <div class="my-6 flex items-center">
          <div class="flex-1 border-t border-slate-600"></div>
          <span class="px-4 text-sm text-gray-400">ou</span>
          <div class="flex-1 border-t border-slate-600"></div>
        </div>

        <!-- Social Login -->
        <div class="space-y-3">
          <UButton color="white" variant="outline" class="w-full">
            <UIcon name="i-lucide-chrome" class="mr-2" />
            Continuer avec Google
          </UButton>
          <UButton color="blue" class="w-full">
            <UIcon name="i-lucide-facebook" class="mr-2" />
            Continuer avec Facebook
          </UButton>
        </div>
      </div>
    </UModal>
  </div>
</template>

<script setup>
// Page meta
definePageMeta({
  layout: 'default'
})

// Reactive data
const isLoginModalOpen = ref(false)
const isLoginMode = ref(true)
const isLoading = ref(false)

const loginForm = reactive({
  email: '',
  password: ''
})

const registerForm = reactive({
  nom: '',
  prenom: '',
  pseudo: '',
  email: '',
  password: '',
  confirmPassword: ''
})

// Methods
async function handleLogin() {
  isLoading.value = true
  try {
    // Simuler une connexion
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Redirection vers le dashboard
    await navigateTo('/liste-mises')
    isLoginModalOpen.value = false
  } catch (error) {
    console.error('Erreur de connexion:', error)
  } finally {
    isLoading.value = false
  }
}

async function handleRegister() {
  isLoading.value = true
  try {
    // Validation basique
    if (registerForm.password !== registerForm.confirmPassword) {
      throw new Error('Les mots de passe ne correspondent pas')
    }
    
    // Simuler une inscription
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Redirection vers le dashboard
    await navigateTo('/liste-mises')
    isLoginModalOpen.value = false
  } catch (error) {
    console.error('Erreur d\'inscription:', error)
  } finally {
    isLoading.value = false
  }
}
</script>

<style scoped>
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in-up {
  animation: fade-in-up 0.6s ease-out;
}

.modal-custom {
  backdrop-filter: blur(10px);
}
</style>