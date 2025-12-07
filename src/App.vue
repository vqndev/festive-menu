<script setup lang="ts">
import { ref } from 'vue'

interface MenuItem {
  name: string
  selected: boolean
}

interface DessertItem {
  name: string
  quantity: number
}

const drinkBases = ref<MenuItem[]>([
  { name: 'Mint', selected: false },
  { name: 'Double Chocolate', selected: false },
  { name: 'Peppermint', selected: false },
  { name: 'Organic', selected: false },
  { name: 'Water', selected: false },
])

interface ToppingItem {
  name: string
  selected: boolean
  hasQuantity?: boolean
  quantity?: number
}

const toppings = ref<ToppingItem[]>([
  { name: 'Dubai Chocolate', selected: false, hasQuantity: true, quantity: 0 },
  { name: 'Sugared Sprinkles', selected: false },
  { name: 'Christmas Sprinkles', selected: false },
  { name: 'Marshmallows', selected: false, hasQuantity: true, quantity: 0 },
  { name: 'Whipped Cream', selected: false },
])

const desserts = ref<DessertItem[]>([
  { name: 'Chocolate Muffins', quantity: 0 },
  { name: 'Cheesy Bread', quantity: 0 },
  { name: 'Pasta', quantity: 0 },
])

const selectBase = (item: MenuItem) => {
  drinkBases.value.forEach(b => b.selected = false)
  item.selected = true
}

const toggleTopping = (item: ToppingItem) => {
  if (!selectedBase()) return
  if (item.hasQuantity) {
    if (item.quantity === 0) {
      item.quantity = 1
      item.selected = true
    }
  } else {
    item.selected = !item.selected
  }
}

const incrementTopping = (item: ToppingItem) => {
  if (!selectedBase()) return
  item.quantity = (item.quantity || 0) + 1
  item.selected = true
  if (item.quantity === 4) {
    alert('Oh hello big back')
  }
}

const decrementTopping = (item: ToppingItem) => {
  if (!selectedBase()) return
  if (item.quantity && item.quantity > 0) {
    item.quantity--
    if (item.quantity === 0) {
      item.selected = false
    }
  }
}

const incrementDessert = (item: DessertItem) => {
  item.quantity++
  if (item.quantity === 4) {
    alert('Oh hello big back')
  }
}

const decrementDessert = (item: DessertItem) => {
  if (item.quantity > 0) {
    item.quantity--
  }
}

const selectedBase = () => drinkBases.value.find(b => b.selected)?.name
const selectedToppings = () => toppings.value.filter(t => t.selected).map(t => t.hasQuantity ? `${t.name} x${t.quantity}` : t.name)
const selectedDesserts = () => desserts.value.filter(d => d.quantity > 0).map(d => `${d.name} x${d.quantity}`)

const submitOrder = () => {
  if (!selectedBase()) {
    alert('Please select a drink base first!')
    return
  }

  const name = prompt('What is your name?')
  if (!name) return

  let summary = `Order for: ${name}\n\nDrink Base: ${selectedBase()}`

  if (selectedToppings().length > 0) {
    summary += `\nToppings: ${selectedToppings().join(', ')}`
  }

  if (selectedDesserts().length > 0) {
    summary += `\nDesserts / Food: ${selectedDesserts().join(', ')}`
  }

  summary += `\n\nShow this order to Auntie Cammy so she can make it for you!`

  alert(summary)
  ratingModalOpen.value = true
}

// Rating modal
const ratingModalOpen = ref(false)
const selectedRating = ref(0)
const ratingError = ref(false)

const selectRating = (rating: number) => {
  selectedRating.value = rating
  if (rating < 5) {
    ratingError.value = true
  } else {
    ratingError.value = false
  }
}

const submitRating = () => {
  ratingModalOpen.value = false
  selectedRating.value = 0
  ratingError.value = false
  tipModalOpen.value = true
}

// Tip modal
const tipModalOpen = ref(false)
const tipSelection = ref('')
const tipError = ref(false)
const showTipInput = ref(false)
const tipAmount = ref(100)
const tipAmountError = ref(false)

const selectTipOption = (option: string) => {
  tipSelection.value = option
  if (option === 'no') {
    tipError.value = true
    showTipInput.value = false
  } else {
    tipError.value = false
    showTipInput.value = true
  }
}

const submitTip = () => {
  if (tipAmount.value < 100) {
    if (tipAmount.value === 67) {
      tipAmountError.value = false
      tipModalOpen.value = false
      tipSelection.value = ''
      showTipInput.value = false
      tipAmount.value = 100
      alert('67, lmaooo')
      commentsModalOpen.value = true
      return
    }
    tipAmountError.value = true
    return
  }
  tipAmountError.value = false
  tipModalOpen.value = false
  tipSelection.value = ''
  showTipInput.value = false
  tipAmount.value = 100
  alert('Thank you for your generous tip! Please come again!')
  commentsModalOpen.value = true
}

// Comments modal
const commentsModalOpen = ref(false)
const commentsWords = ['This', 'is', 'the', 'Finest,', 'greatest,', 'top,', 'supreme,', 'optimal,', 'prime,', 'premier,', 'leading,', 'excellent,', 'exceptional,', 'outstanding,', 'superb,', 'superior,', 'first-rate,', 'first-class,', 'unbeatable,', 'ultimate,', 'peak,', 'paramount,', 'optimum,', 'ideal,', 'perfect,', 'choice,', 'select,', 'foremost,', 'a-one,', 'top-notch,', 'top-tier,', 'matchless,', 'unmatched,', 'unrivaled,', 'unsurpassed,', 'elite,', 'pinnacle,', 'crowning,', 'splendid,', 'terrific,', 'marvelous,', 'stellar,', 'distinguished,', 'notable,', 'first,', 'chief,', 'major,', 'preferable,', 'advantageous,', 'leading-edge,', 'benchmark,', 'gold-standard,', 'blue-ribbon,', 'hallmark,', 'preeminent,', 'prime-quality,', 'optimum-level,', 'prize-winning,', 'renowned,', 'celebrated,', 'superlative,', 'admirable,', 'noteworthy,', 'glorious,', 'grand,', 'top-of-the-line,', 'high-caliber', 'restaurant', 'ever!']
const commentsWordIndex = ref(0)
const commentsText = ref('')

const handleCommentsKeydown = (event: KeyboardEvent) => {
  // Only respond to actual character keys or backspace
  if (event.key === 'Backspace') {
    event.preventDefault()
    submitComments()
    return
  }

  // Ignore non-printable keys (like Shift, Ctrl, Meta, Tab, etc.)
  if (event.key.length !== 1) {
    return
  }

  event.preventDefault()

  // For any character key, add the next word
  if (commentsWordIndex.value < commentsWords.length) {
    if (commentsWordIndex.value > 0) {
      commentsText.value += ' '
    }
    commentsText.value += commentsWords[commentsWordIndex.value]
    commentsWordIndex.value++
  }
}

const submitComments = () => {
  commentsModalOpen.value = false
  commentsWordIndex.value = 0
  commentsText.value = ''
  alert('Thank you for submitting your concerns!')
}

const resetOrder = () => {
  drinkBases.value.forEach(b => b.selected = false)
  toppings.value.forEach(t => {
    t.selected = false
    if (t.hasQuantity) t.quantity = 0
  })
  desserts.value.forEach(d => d.quantity = 0)
}

// Gallery
const galleryImages = [
  '/gallery-1.jpg',
  '/gallery-2.jpg',
  '/gallery-3.jpg',
  '/gallery-4.png',
]
const lightboxOpen = ref(false)
const lightboxImage = ref('')

const openLightbox = (image: string) => {
  lightboxImage.value = image
  lightboxOpen.value = true
}

const closeLightbox = () => {
  lightboxOpen.value = false
}

// Theme
const currentTheme = ref<'christmas' | 'cora'>('christmas')
</script>

<template>
  <div
    class="min-h-screen py-8 px-4 overflow-hidden relative transition-all duration-500"
    :style="currentTheme === 'cora'
      ? 'background: url(/background.jpg) center/cover no-repeat fixed;'
      : 'background: linear-gradient(to bottom, rgba(0,0,0,0.3), rgba(0,0,0,0) 30%, rgba(0,0,0,0) 70%, rgba(0,0,0,0.4)), repeating-linear-gradient(45deg, #7f1d1d, #7f1d1d 50px, #14532d 50px, #14532d 100px);'"
  >
    <!-- Theme Selector -->
    <div class="fixed top-0 left-0 right-0 z-50 bg-black/50 backdrop-blur-sm py-2 px-4 flex justify-center gap-4">
      <button
        @click="currentTheme = 'christmas'"
        class="px-4 py-2 rounded-lg font-bold transition-all duration-200"
        :class="currentTheme === 'christmas' ? 'bg-red-600 text-white' : 'bg-white/20 text-white hover:bg-white/30'"
      >
        Christmas
      </button>
      <button
        @click="currentTheme = 'cora'"
        class="px-4 py-2 rounded-lg font-bold transition-all duration-200"
        :class="currentTheme === 'cora' ? 'bg-pink-500 text-white' : 'bg-white/20 text-white hover:bg-white/30'"
      >
        Cora
      </button>
    </div>

    <!-- Snowflakes -->
    <div v-if="currentTheme === 'christmas'" class="fixed inset-0 pointer-events-none overflow-hidden">
      <svg v-for="i in 50" :key="i" class="snowflake absolute text-white" :style="{
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 5}s`,
        opacity: 0.3 + Math.random() * 0.4,
        fontSize: `${12 + Math.random() * 20}px`
      }" viewBox="0 0 24 24" fill="currentColor" :width="12 + Math.random() * 16" :height="12 + Math.random() * 16">
        <path d="M12 0L12 24M0 12L24 12M3.5 3.5L20.5 20.5M20.5 3.5L3.5 20.5" stroke="currentColor" stroke-width="1.5" fill="none"/>
        <circle cx="12" cy="12" r="2" fill="currentColor"/>
      </svg>
    </div>

    <!-- Falling Bows -->
    <div v-if="currentTheme === 'christmas'" class="fixed inset-0 pointer-events-none overflow-hidden">
      <img v-for="i in 30" :key="'bow-' + i" src="/bow.png" alt="" class="bow absolute" :style="{
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 8}s`,
        opacity: 0.7 + Math.random() * 0.3,
        width: `${20 + Math.random() * 40}px`
      }" />
    </div>

    <!-- Candy Cane Top Left -->
    <div v-if="currentTheme === 'christmas'" class="fixed top-4 left-0 pointer-events-none z-10">
      <img src="/candy-cane.png" alt="Candy Cane" class="w-16 h-auto drop-shadow-lg" style="transform: rotate(-30deg);" />
    </div>

    <!-- Candy Cane Top Right -->
    <div v-if="currentTheme === 'christmas'" class="fixed top-4 right-0 pointer-events-none z-10">
      <img src="/candy-cane.png" alt="Candy Cane" class="w-16 h-auto drop-shadow-lg" style="transform: rotate(-30deg);" />
    </div>

    <!-- Hot Chocolate Mug - Bottom Right -->
    <div v-if="currentTheme === 'christmas'" class="fixed bottom-4 right-4 pointer-events-none z-10">
      <svg width="300" height="300" viewBox="0 0 100 100" class="drop-shadow-xl">
        <!-- Steam -->
        <path d="M35 25 Q40 15 35 5" stroke="#ffffff" stroke-width="2" fill="none" opacity="0.5" class="animate-pulse"/>
        <path d="M50 20 Q55 10 50 0" stroke="#ffffff" stroke-width="2" fill="none" opacity="0.5" class="animate-pulse" style="animation-delay: 0.3s"/>
        <path d="M65 25 Q70 15 65 5" stroke="#ffffff" stroke-width="2" fill="none" opacity="0.5" class="animate-pulse" style="animation-delay: 0.6s"/>
        <!-- Mug body -->
        <rect x="20" y="30" width="60" height="55" rx="8" fill="#e74c3c"/>
        <rect x="25" y="35" width="50" height="45" rx="5" fill="#c0392b"/>
        <!-- Hot chocolate -->
        <ellipse cx="50" cy="40" rx="22" ry="8" fill="#5D4037"/>
        <!-- Marshmallows -->
        <ellipse cx="38" cy="38" rx="8" ry="6" fill="#fff5e6" stroke="#ffe4c4" stroke-width="1"/>
        <ellipse cx="55" cy="36" rx="7" ry="5" fill="#fff5e6" stroke="#ffe4c4" stroke-width="1"/>
        <ellipse cx="62" cy="42" rx="6" ry="5" fill="#fff5e6" stroke="#ffe4c4" stroke-width="1"/>
        <!-- Mug handle -->
        <path d="M80 45 Q95 45 95 60 Q95 75 80 75" stroke="#e74c3c" stroke-width="8" fill="none"/>
        <path d="M80 50 Q88 50 88 60 Q88 70 80 70" stroke="#c0392b" stroke-width="4" fill="none"/>
        <!-- Cute face on mug -->
        <circle cx="40" cy="60" r="3" fill="#2c1810"/>
        <circle cx="55" cy="60" r="3" fill="#2c1810"/>
        <path d="M43 70 Q47 75 52 70" stroke="#2c1810" stroke-width="2" fill="none" stroke-linecap="round"/>
        <!-- Blush -->
        <ellipse cx="35" cy="65" rx="4" ry="2" fill="#ff9999" opacity="0.5"/>
        <ellipse cx="60" cy="65" rx="4" ry="2" fill="#ff9999" opacity="0.5"/>
      </svg>
    </div>

    <!-- Candy Cane Bottom Left -->
    <div v-if="currentTheme === 'christmas'" class="fixed bottom-4 left-0 pointer-events-none z-10">
      <img src="/candy-cane.png" alt="Candy Cane" class="w-16 h-auto drop-shadow-lg" style="transform: rotate(-30deg);" />
    </div>

    <!-- Candy Cane Bottom Right -->
    <div v-if="currentTheme === 'christmas'" class="fixed bottom-4 right-24 pointer-events-none z-10">
      <img src="/candy-cane.png" alt="Candy Cane" class="w-16 h-auto drop-shadow-lg" style="transform: rotate(-30deg);" />
    </div>

    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="text-center mb-8 mt-12">
        <h1 :class="[
          'text-3xl font-bold mb-2 italic tracking-tight',
          currentTheme === 'cora' ? 'text-blue-800' : 'text-white'
        ]">Starflower Family Hot Cocoa Menu</h1>
      </div>

      <!-- 2x2 Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <!-- Drink Bases - Top Left -->
        <div :class="[
          'backdrop-blur-sm rounded-2xl p-6 border',
          currentTheme === 'cora' ? 'bg-white/70 border-blue-200' : 'bg-white/10 border-white/20'
        ]">
          <h2 :class="[
            'text-2xl font-bold mb-4',
            currentTheme === 'cora' ? 'text-blue-600' : 'text-red-300'
          ]">Drink Base</h2>
          <div class="space-y-3">
            <button
              v-for="item in drinkBases"
              :key="item.name"
              @click="selectBase(item)"
              :class="[
                'w-full text-left px-4 py-3 rounded-xl transition-all duration-200 flex items-center gap-3',
                item.selected
                  ? (currentTheme === 'cora' ? 'bg-blue-500 text-white shadow-lg shadow-blue-500/30 scale-[1.02]' : 'bg-red-500 text-white shadow-lg shadow-red-500/30 scale-[1.02]')
                  : (currentTheme === 'cora' ? 'bg-blue-50 text-blue-800 hover:bg-blue-100' : 'bg-white/5 text-white hover:bg-white/10')
              ]"
            >
                            {{ item.name }}
            </button>
          </div>
        </div>

        <!-- Toppings - Top Right -->
        <div :class="[
          'backdrop-blur-sm rounded-2xl p-6 border',
          currentTheme === 'cora' ? 'bg-white/70 border-blue-200' : 'bg-white/10 border-white/20',
          { 'opacity-50': !selectedBase() }
        ]">
          <h2 :class="[
            'text-2xl font-bold mb-4',
            currentTheme === 'cora' ? 'text-blue-500' : 'text-green-400'
          ]">Toppings <span :class="[
            'text-lg font-normal',
            currentTheme === 'cora' ? 'text-blue-400' : 'text-white/60'
          ]">(optional)</span></h2>
          <p v-if="!selectedBase()" :class="[
            'text-sm mb-3',
            currentTheme === 'cora' ? 'text-blue-400' : 'text-white/60'
          ]">Please select a drink base first</p>
          <div class="space-y-3">
            <!-- Toppings with quantity (Dubai Chocolate, Marshmallows) -->
            <template v-for="item in toppings" :key="item.name">
              <div
                v-if="item.hasQuantity"
                @click="toggleTopping(item)"
                :class="[
                  'w-full px-4 py-3 rounded-xl transition-all duration-200 flex items-center justify-between',
                  item.selected
                    ? (currentTheme === 'cora' ? 'bg-blue-400 text-white shadow-lg shadow-blue-400/30' : 'bg-green-600 text-white shadow-lg shadow-green-500/30')
                    : (currentTheme === 'cora' ? 'bg-blue-50 text-blue-800' : 'bg-white/5 text-white'),
                  selectedBase() ? (currentTheme === 'cora' ? 'hover:bg-blue-100 cursor-pointer' : 'hover:bg-white/10 cursor-pointer') : 'cursor-not-allowed opacity-50'
                ]"
              >
                <span>{{ item.name }}</span>
                <div v-if="selectedBase()" class="flex items-center gap-2">
                  <button
                    @click.stop="decrementTopping(item)"
                    :class="[
                      'w-8 h-8 rounded-full flex items-center justify-center font-bold text-lg transition-all',
                      item.selected
                        ? 'bg-white/30 hover:bg-white/50'
                        : (currentTheme === 'cora' ? 'bg-blue-200 hover:bg-blue-300 text-blue-800' : 'bg-white/10 hover:bg-white/20')
                    ]"
                  >
                    -
                  </button>
                  <span class="w-8 text-center font-bold">{{ item.quantity }}</span>
                  <button
                    @click.stop="incrementTopping(item)"
                    :class="[
                      'w-8 h-8 rounded-full flex items-center justify-center font-bold text-lg transition-all',
                      item.selected
                        ? 'bg-white/30 hover:bg-white/50'
                        : (currentTheme === 'cora' ? 'bg-blue-200 hover:bg-blue-300 text-blue-800' : 'bg-white/10 hover:bg-white/20')
                    ]"
                  >
                    +
                  </button>
                </div>
              </div>
              <!-- Regular toppings (toggle only) -->
              <button
                v-else
                @click="toggleTopping(item)"
                :disabled="!selectedBase()"
                :class="[
                  'w-full text-left px-4 py-3 rounded-xl transition-all duration-200 flex items-center gap-3',
                  item.selected
                    ? (currentTheme === 'cora' ? 'bg-blue-400 text-white shadow-lg shadow-blue-400/30 scale-[1.02]' : 'bg-green-600 text-white shadow-lg shadow-green-500/30 scale-[1.02]')
                    : (currentTheme === 'cora' ? 'bg-blue-50 text-blue-800' : 'bg-white/5 text-white'),
                  selectedBase() ? (currentTheme === 'cora' ? 'hover:bg-blue-100 cursor-pointer' : 'hover:bg-white/10 cursor-pointer') : 'cursor-not-allowed'
                ]"
              >
                {{ item.name }}
              </button>
            </template>
          </div>
        </div>

        <!-- Desserts - Bottom Left -->
        <div :class="[
          'backdrop-blur-sm rounded-2xl p-6 border',
          currentTheme === 'cora' ? 'bg-white/70 border-blue-200' : 'bg-white/10 border-white/20'
        ]">
          <h2 :class="[
            'text-2xl font-bold mb-4 flex items-center gap-2',
            currentTheme === 'cora' ? 'text-blue-600' : 'text-red-500'
          ]">
            Desserts / Food <span :class="currentTheme === 'cora' ? 'text-blue-400' : 'text-red-400'">&#10084;</span>
          </h2>
          <div class="space-y-3">
            <div
              v-for="item in desserts"
              :key="item.name"
              @click="item.quantity === 0 ? incrementDessert(item) : null"
              :class="[
                'w-full px-4 py-3 rounded-xl transition-all duration-200 flex items-center justify-between',
                item.quantity > 0
                  ? (currentTheme === 'cora' ? 'bg-blue-400 text-white shadow-lg shadow-blue-400/30' : 'bg-green-600 text-white shadow-lg shadow-green-500/30')
                  : (currentTheme === 'cora' ? 'bg-blue-50 text-blue-800 hover:bg-blue-100 cursor-pointer' : 'bg-white/5 text-white hover:bg-white/10 cursor-pointer')
              ]"
            >
              <span>{{ item.name }}</span>
              <div class="flex items-center gap-2">
                <button
                  @click.stop="decrementDessert(item)"
                  :class="[
                    'w-8 h-8 rounded-full flex items-center justify-center font-bold text-lg transition-all',
                    item.quantity > 0
                      ? 'bg-white/30 hover:bg-white/50'
                      : (currentTheme === 'cora' ? 'bg-blue-200 hover:bg-blue-300 text-blue-800' : 'bg-white/10 hover:bg-white/20')
                  ]"
                >
                  -
                </button>
                <span class="w-8 text-center font-bold">{{ item.quantity }}</span>
                <button
                  @click.stop="incrementDessert(item)"
                  :class="[
                    'w-8 h-8 rounded-full flex items-center justify-center font-bold text-lg transition-all',
                    item.quantity > 0
                      ? 'bg-white/30 hover:bg-white/50'
                      : (currentTheme === 'cora' ? 'bg-blue-200 hover:bg-blue-300 text-blue-800' : 'bg-white/10 hover:bg-white/20')
                  ]"
                >
                  +
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Gallery - Bottom Right -->
        <div :class="[
          'backdrop-blur-sm rounded-2xl p-6 border',
          currentTheme === 'cora' ? 'bg-white/70 border-blue-200' : 'bg-white/10 border-white/20'
        ]">
          <h2 :class="[
            'text-2xl font-bold mb-4',
            currentTheme === 'cora' ? 'text-blue-500' : 'text-green-400'
          ]">Gallery</h2>
          <div class="grid grid-cols-3 gap-2">
            <img
              v-for="(image, index) in galleryImages"
              :key="index"
              :src="image"
              alt="Gallery image"
              class="w-full h-20 object-cover rounded-lg cursor-pointer hover:opacity-80 transition-opacity hover:scale-105 transform duration-200"
              @click="openLightbox(image)"
            />
          </div>
        </div>
      </div>

      <!-- Lightbox -->
      <div
        v-if="lightboxOpen"
        class="fixed inset-0 bg-black/90 z-50 flex items-center justify-center p-4"
        @click="closeLightbox"
      >
        <button
          class="absolute top-4 right-4 text-white text-4xl hover:text-gray-300"
          @click="closeLightbox"
        >
          &times;
        </button>
        <img
          :src="lightboxImage"
          alt="Lightbox image"
          class="max-w-full max-h-full object-contain rounded-lg"
          @click.stop
        />
      </div>

      <!-- Buttons -->
      <div class="mt-6 flex justify-center gap-4">
        <button
          @click="resetOrder"
          :class="[
            'font-bold py-4 px-8 rounded-2xl text-xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200 border',
            currentTheme === 'cora'
              ? 'bg-white/70 text-blue-600 border-blue-200 hover:bg-white'
              : 'bg-white/10 text-white border-white/20 hover:bg-white/20'
          ]"
        >
          Reset
        </button>
        <button
          @click="submitOrder"
          :class="[
            'text-white font-bold py-4 px-12 rounded-2xl text-xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200 border border-white/20',
            currentTheme === 'cora'
              ? 'bg-gradient-to-r from-blue-500 to-blue-600'
              : 'bg-gradient-to-r from-red-600 to-green-600'
          ]"
        >
          Submit Order
        </button>
      </div>

      <!-- Footer -->
      <div :class="[
        'text-center mt-8 text-sm',
        currentTheme === 'cora' ? 'text-blue-400' : 'text-white/40'
      ]">
        Happy Holidays!
      </div>

      <!-- Rating Modal -->
      <div
        v-if="ratingModalOpen"
        class="fixed inset-0 bg-black/80 z-50 flex items-center justify-center p-4"
      >
        <div class="bg-white rounded-2xl p-8 max-w-md w-full text-center" @click.stop>
          <h2 class="text-2xl font-bold text-gray-800 mb-6">Rate This Website</h2>

          <div class="flex justify-center gap-2 mb-6">
            <button
              v-for="star in 5"
              :key="star"
              @click="selectRating(star)"
              class="text-4xl transition-transform hover:scale-110"
              :class="star <= selectedRating ? 'text-yellow-400' : 'text-gray-300'"
            >
              &#9733;
            </button>
          </div>

          <p v-if="ratingError" class="text-red-500 mb-4">This is not a valid option.</p>

          <button
            v-if="selectedRating === 5"
            @click="submitRating"
            class="bg-gradient-to-r from-red-600 to-green-600 text-white font-bold py-3 px-8 rounded-xl text-lg shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200"
          >
            Submit
          </button>
        </div>
      </div>

      <!-- Tip Modal -->
      <div
        v-if="tipModalOpen"
        class="fixed inset-0 bg-black/80 z-50 flex items-center justify-center p-4"
      >
        <div class="bg-white rounded-2xl p-8 max-w-md w-full text-center" @click.stop>
          <h2 class="text-2xl font-bold text-gray-800 mb-6">Would you like to leave a tip?</h2>

          <div class="flex justify-center gap-4 mb-6">
            <button
              @click="selectTipOption('yes')"
              class="py-3 px-8 rounded-xl text-lg font-bold transition-all duration-200"
              :class="tipSelection === 'yes' ? 'bg-green-500 text-white' : 'bg-gray-200 text-gray-800 hover:bg-gray-300'"
            >
              Yes
            </button>
            <button
              @click="selectTipOption('no')"
              class="py-3 px-8 rounded-xl text-lg font-bold transition-all duration-200"
              :class="tipSelection === 'no' ? 'bg-red-500 text-white' : 'bg-gray-200 text-gray-800 hover:bg-gray-300'"
            >
              No
            </button>
          </div>

          <p v-if="tipError" class="text-red-500 mb-4">This is not a valid option.</p>

          <div v-if="showTipInput" class="mb-6">
            <label class="block text-gray-700 mb-2">Tip Amount ($)</label>
            <input
              v-model.number="tipAmount"
              type="number"
              min="0"
              class="w-full px-4 py-3 text-xl text-center border-2 border-gray-300 rounded-xl focus:border-green-500 focus:outline-none"
            />
            <p v-if="tipAmountError" class="text-red-500 mt-2">This is not a valid value. Minimum tip is $100.</p>
          </div>

          <button
            v-if="showTipInput"
            @click="submitTip"
            class="bg-gradient-to-r from-red-600 to-green-600 text-white font-bold py-3 px-8 rounded-xl text-lg shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200"
          >
            Submit Tip
          </button>
        </div>
      </div>

      <!-- Comments Modal -->
      <div
        v-if="commentsModalOpen"
        class="fixed inset-0 bg-black/80 z-50 flex items-center justify-center p-4"
      >
        <div class="bg-white rounded-2xl p-8 max-w-md w-full text-center" @click.stop>
          <h2 class="text-2xl font-bold text-gray-800 mb-6">Comments & Concerns</h2>

          <textarea
            :value="commentsText"
            @keydown="handleCommentsKeydown"
            placeholder="Type your feedback..."
            class="w-full px-4 py-3 text-lg border-2 border-gray-300 rounded-xl focus:border-green-500 focus:outline-none resize-none h-32"
          ></textarea>

          <p class="text-gray-500 text-sm mt-2 mb-4">Press any key to type. Press backspace to submit.</p>

          <button
            @click="submitComments"
            class="bg-gradient-to-r from-red-600 to-green-600 text-white font-bold py-3 px-8 rounded-xl text-lg shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200"
          >
            Submit
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@keyframes snowfall {
  0% {
    transform: translateY(-10vh) rotate(0deg);
  }
  100% {
    transform: translateY(110vh) rotate(360deg);
  }
}

.snowflake {
  animation: snowfall 8s linear infinite;
}

@keyframes bowfall {
  0% {
    transform: translateY(-10vh) rotate(0deg);
  }
  50% {
    transform: translateY(50vh) rotate(20deg);
  }
  100% {
    transform: translateY(110vh) rotate(-10deg);
  }
}

.bow {
  animation: bowfall 12s ease-in-out infinite;
}
</style>
