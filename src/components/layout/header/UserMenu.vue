<template>
  <div class="relative" ref="dropdownRef">

    <button class="flex items-center text-gray-700 dark:text-gray-400" @click.prevent="toggleDropdown">
      <span class="block mr-1 font-medium text-theme-sm">
        {{ userData.username || 'User' }}
      </span>
      <ChevronDownIcon :class="{ 'rotate-180': dropdownOpen }" />
    </button>

    <div v-if="dropdownOpen"
      class="absolute right-0 mt-[17px] flex w-[260px] flex-col rounded-2xl border border-gray-200 bg-white p-3 shadow-theme-lg dark:border-gray-800 dark:bg-gray-dark z-50">

      <div class="px-3 py-2">
        <span class="block font-medium text-gray-700 text-theme-sm dark:text-gray-400">
          {{ userData.username || 'Pengguna' }}
        </span>
        <span class="mt-0.5 block text-theme-xs text-gray-500 dark:text-gray-400 truncate">
          {{ userData.phone || 'No Phone' }}
        </span>
      </div>

      <hr class="border-gray-100 dark:border-gray-800 my-1">

      <button @click="signOut"
        class="flex w-full items-center gap-3 px-3 py-2 mt-1 font-medium text-gray-700 rounded-lg group text-theme-sm hover:bg-red-50 hover:text-red-700 dark:text-gray-400 dark:hover:bg-white/5 dark:hover:text-red-400 transition">
        <LogoutIcon class="text-gray-500 group-hover:text-red-700 dark:group-hover:text-red-400" />
        Sign out
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, reactive } from 'vue'
import { useRouter } from 'vue-router'
import Swal from 'sweetalert2'

// Import Icons (Sesuaikan path icon Anda)
import { UserCircleIcon, ChevronDownIcon, LogoutIcon, SettingsIcon, InfoCircleIcon } from '@/icons'

const router = useRouter()
const dropdownOpen = ref(false)
const dropdownRef = ref(null)

// State untuk data user yang sedang login
const userData = reactive({
  username: '',
  phone: '',
  role: ''
})

const menuItems = [
  { href: '/profile', icon: UserCircleIcon, text: 'Edit profile' },
  { href: '/settings', icon: SettingsIcon, text: 'Account settings' },
]

const toggleDropdown = () => {
  dropdownOpen.value = !dropdownOpen.value
}

const closeDropdown = () => {
  dropdownOpen.value = false
}

// --- FUNGSI LOGOUT ---
const signOut = async () => {
  closeDropdown()

  // Tampilkan konfirmasi (Opsional, bisa langsung logout juga)
  const result = await Swal.fire({
    title: 'Keluar?',
    text: "Anda akan diarahkan ke halaman login",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#3085d6',
    cancelButtonColor: '#d33',
    confirmButtonText: 'Ya, Keluar',
    cancelButtonText: 'Batal'
  })

  if (result.isConfirmed) {
    // 1. Hapus Token & Data User dari LocalStorage
    localStorage.removeItem('token')
    localStorage.removeItem('user') // Jika Anda menyimpan object user saat login

    // 2. Notifikasi Sukses
    await Swal.fire({
      icon: 'success',
      title: 'Berhasil Keluar',
      showConfirmButton: false,
      timer: 1500
    })

    // 3. Redirect ke Halaman Login
    router.push('/signin')
  }
}

// Logic Click Outside (Menutup dropdown jika klik di luar)
const handleClickOutside = (event) => {
  if (dropdownRef.value && !dropdownRef.value.contains(event.target)) {
    closeDropdown()
  }
}

// Ambil Data User saat komponen dipasang
onMounted(() => {
  document.addEventListener('click', handleClickOutside)

  // Ambil data user dari localStorage (asumsi Anda menyimpannya saat Login)
  // Contoh penyimpanan saat login: localStorage.setItem('user', JSON.stringify(response.data.user))
  const storedUser = localStorage.getItem('user')
  if (storedUser) {
    try {
      const parsedUser = JSON.parse(storedUser)
      userData.username = parsedUser.username || 'User'
      userData.phone = parsedUser.phone || ''
      userData.role = parsedUser.role || ''
    } catch (e) {
      console.error("Gagal parsing data user", e)
    }
  }
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>