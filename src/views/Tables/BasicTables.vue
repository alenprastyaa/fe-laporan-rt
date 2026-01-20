<template>
  <AdminLayout>
    <div class="space-y-6">

      <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
        <div>
          <h1 class="text-2xl font-bold text-gray-800 dark:text-white tracking-tight">Manajemen Pengguna</h1>
          <p class="text-sm text-gray-500 dark:text-gray-400 mt-1">Kelola data warga dan hak akses sistem.</p>
        </div>

        <button @click="openModal('add')"
          class="group relative px-6 py-2.5 bg-gradient-to-r from-blue-600 to-indigo-600 text-white rounded-full shadow-lg shadow-blue-500/30 hover:shadow-blue-500/50 hover:scale-[1.02] transition-all duration-300 font-medium text-sm flex items-center gap-2 overflow-hidden">
          <span
            class="absolute inset-0 w-full h-full bg-white/20 group-hover:translate-x-full transition-transform duration-500 ease-out -skew-x-12 origin-left"></span>
          <svg class="w-5 h-5 relative z-10" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
          </svg>
          <span class="relative z-10">Tambah User Baru</span>
        </button>
      </div>

      <div
        class="bg-white dark:bg-gray-800 rounded-2xl shadow-sm border border-gray-100 dark:border-gray-700 overflow-hidden">

        <div
          class="p-5 border-b border-gray-100 dark:border-gray-700 bg-gray-50/50 dark:bg-gray-800/50 flex flex-col sm:flex-row justify-between gap-4">
          <div class="relative w-full sm:w-72 group">
            <span
              class="absolute inset-y-0 left-0 flex items-center pl-3 transition-colors group-focus-within:text-blue-500 text-gray-400">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
              </svg>
            </span>
            <input v-model="searchQuery" @input="handleSearch" type="text" placeholder="Cari Username atau No. HP..."
              class="w-full pl-10 pr-4 py-2.5 bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-600 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 transition-all dark:text-white placeholder-gray-400">
          </div>

        </div>

        <div class="overflow-x-auto">
          <table class="min-w-full align-middle">
            <thead>
              <tr class="bg-gray-50/80 dark:bg-gray-700/30 border-b border-gray-100 dark:border-gray-700">
                <th
                  class="px-6 py-4 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  User Profile</th>
                <th
                  class="px-6 py-4 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Kontak</th>
                <th
                  class="px-6 py-4 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Role</th>
                <th
                  class="px-6 py-4 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Bergabung</th>
                <th
                  class="px-6 py-4 text-right text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Aksi</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-100 dark:divide-gray-700 bg-white dark:bg-gray-800">
              <tr v-if="isLoading">
                <td colspan="5" class="px-6 py-12 text-center">
                  <div class="flex flex-col items-center justify-center">
                    <svg class="animate-spin h-8 w-8 text-blue-500 mb-2" xmlns="http://www.w3.org/2000/svg" fill="none"
                      viewBox="0 0 24 24">
                      <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                      <path class="opacity-75" fill="currentColor"
                        d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                      </path>
                    </svg>
                    <span class="text-gray-500 text-sm">Sedang memuat data...</span>
                  </div>
                </td>
              </tr>
              <tr v-else-if="users.length === 0">
                <td colspan="5" class="px-6 py-12 text-center text-gray-500">
                  <p class="text-base font-medium">Data tidak ditemukan</p>
                  <p class="text-xs mt-1">Coba kata kunci pencarian lain.</p>
                </td>
              </tr>

              <tr v-else v-for="user in users" :key="user.id"
                class="group hover:bg-blue-50/50 dark:hover:bg-blue-900/10 transition-colors duration-200">
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center gap-4">
                    <div class="relative">
                      <div
                        class="w-10 h-10 rounded-full overflow-hidden ring-2 ring-gray-100 dark:ring-gray-700 group-hover:ring-blue-200 transition-all">
                        <img
                          :src="`https://ui-avatars.com/api/?name=${user.username}&background=random&color=fff&bold=true`"
                          class="w-full h-full object-cover" alt="Avatar" />
                      </div>
                      <span
                        class="absolute bottom-0 right-0 w-2.5 h-2.5 bg-green-500 border-2 border-white dark:border-gray-800 rounded-full"></span>
                    </div>
                    <div>
                      <div class="text-sm font-semibold text-gray-900 dark:text-white">{{ user.username }}</div>
                      <div class="text-xs text-gray-500 dark:text-gray-400 hidden sm:block">ID: #{{ user.id }}</div>
                    </div>
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-600 dark:text-gray-300 flex items-center gap-2">
                    <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z">
                      </path>
                    </svg>
                    {{ user.phone || 'Belum diisi' }}
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <span :class="[
                    'px-3 py-1 rounded-full text-xs font-semibold border',
                    user.role === 'admin'
                      ? 'bg-purple-50 text-purple-700 border-purple-100 dark:bg-purple-900/30 dark:text-purple-300 dark:border-purple-800'
                      : 'bg-blue-50 text-blue-700 border-blue-100 dark:bg-blue-900/30 dark:text-blue-300 dark:border-blue-800'
                  ]">
                    {{ user.role === 'admin' ? 'Administrator' : 'Warga' }}
                  </span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                  {{ formatDate(user.created_at) }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                  <div
                    class="flex items-center justify-end gap-2 opacity-80 group-hover:opacity-100 transition-opacity">
                    <button @click="openModal('edit', user)"
                      class="p-2 bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 text-gray-600 dark:text-gray-300 rounded-lg hover:border-blue-500 hover:text-blue-600 hover:shadow-md transition-all duration-200"
                      title="Edit Data">
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                          d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z">
                        </path>
                      </svg>
                    </button>
                    <button @click="deleteUser(user.id)"
                      class="p-2 bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 text-gray-600 dark:text-gray-300 rounded-lg hover:border-red-500 hover:text-red-600 hover:shadow-md transition-all duration-200"
                      title="Hapus User">
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                          d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                        </path>
                      </svg>
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div
          class="border-t border-gray-100 dark:border-gray-700 bg-gray-50/50 dark:bg-gray-800/50 px-6 py-4 flex flex-col sm:flex-row items-center justify-between gap-4">
          <span class="text-sm text-gray-500 dark:text-gray-400">
            Menampilkan halaman <span class="font-semibold text-gray-700 dark:text-white">{{ pagination.currentPage
              }}</span> dari {{ pagination.totalPage }}
          </span>
          <div class="flex items-center gap-2">
            <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage <= 1"
              class="px-4 py-2 text-sm font-medium bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors text-gray-700 dark:text-white shadow-sm">
              Previous
            </button>
            <button @click="changePage(pagination.currentPage + 1)"
              :disabled="pagination.currentPage >= pagination.totalPage"
              class="px-4 py-2 text-sm font-medium bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors text-gray-700 dark:text-white shadow-sm">
              Next
            </button>
          </div>
        </div>
      </div>

      <Transition name="modal-fade">
        <div v-if="showModal" class="fixed inset-0 z-50 flex items-center justify-center p-4">
          <div class="absolute inset-0 bg-gray-900/60 backdrop-blur-sm transition-opacity" @click="closeModal"></div>

          <div
            class="relative bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl border border-gray-100 dark:border-gray-700 overflow-hidden transform transition-all scale-100">

            <div
              class="px-6 py-5 border-b border-gray-100 dark:border-gray-700 flex justify-between items-center bg-gray-50/50 dark:bg-gray-800/50">
              <div>
                <h3 class="text-lg font-bold text-gray-800 dark:text-white">
                  {{ isEditMode ? 'Edit Profil User' : 'TambahUser Baru' }}</h3>
                <p class="text-xs text-gray-500 mt-0.5">Pastikan data yang diinput sudah benar.</p>
              </div>
              <button @click="closeModal"
                class="p-1 rounded-full text-gray-400 hover:text-gray-600 hover:bg-gray-100 dark:hover:bg-gray-700 transition">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>

            <form @submit.prevent="submitForm" class="p-6 space-y-5">
              <div class="space-y-4">
                <div>
                  <label
                    class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Username</label>
                  <input v-model="form.username" type="text"
                    class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition-all dark:text-white placeholder-gray-400 text-sm"
                    placeholder="Masukkan nama pengguna" required>
                </div>

                <div>
                  <label
                    class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Nomor
                    HP</label>
                  <input v-model="form.phone" type="text"
                    class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition-all dark:text-white placeholder-gray-400 text-sm"
                    placeholder="0812...">
                </div>

                <div>
                  <label
                    class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Role
                    Akses</label>
                  <div class="relative">
                    <select v-model="form.role"
                      class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition-all dark:text-white text-sm appearance-none">
                      <option value="user">Warga</option>
                    </select>
                    <div class="absolute inset-y-0 right-0 flex items-center px-3 pointer-events-none text-gray-500">
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                      </svg>
                    </div>
                  </div>
                </div>

                <div>
                  <label
                    class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">
                    Password {{ isEditMode ? '(Opsional)' : '' }}
                  </label>
                  <input v-model="form.password" type="password"
                    class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition-all dark:text-white placeholder-gray-400 text-sm"
                    placeholder="••••••••" :required="!isEditMode">
                </div>
              </div>

              <div class="pt-2 flex gap-3">
                <button type="button" @click="closeModal"
                  class="flex-1 px-4 py-2.5 border border-gray-200 dark:border-gray-600 rounded-xl text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-700 font-medium transition-colors text-sm">
                  Batal
                </button>
                <button type="submit"
                  class="flex-1 px-4 py-2.5 bg-gradient-to-r from-blue-600 to-indigo-600 text-white rounded-xl hover:shadow-lg hover:shadow-blue-500/30 font-medium transition-all duration-300 text-sm transform active:scale-95">
                  {{ isSubmitting ? 'Menyimpan...' : 'Simpan Perubahan' }}
                </button>
              </div>
            </form>
          </div>
        </div>
      </Transition>

    </div>
  </AdminLayout>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import AdminLayout from '@/components/layout/AdminLayout.vue'

// State Data
const users = ref([])
const isLoading = ref(false)
const searchQuery = ref('')
const selectedRole = ref('')
let searchTimeout = null

// State Pagination
const pagination = reactive({ currentPage: 1, limit: 8, totalData: 0, totalPage: 1 })

// State Modal
const showModal = ref(false)
const isEditMode = ref(false)
const isSubmitting = ref(false)
const editId = ref(null)

// Form Data
const form = reactive({
  username: '',
  phone: '',
  role: 'user',
  password: '',
  rt: ''
})

const fetchUsers = async () => {
  isLoading.value = true
  const currentRt = localStorage.getItem('rt')

  try {
    const token = localStorage.getItem('token')
    const res = await axios.get('https://alentest.my.id/laporan/api/auth/users', {
      headers: { Authorization: `Bearer ${token}` },
      params: {
        page: pagination.currentPage,
        limit: pagination.limit,
        search: searchQuery.value,
        role: selectedRole.value,
        rt: currentRt
      }
    })
    if (res.data.status === 'success') {
      users.value = res.data.data
      pagination.currentPage = res.data.pagination.currentPage
      pagination.totalPage = res.data.pagination.totalPage
    }
  } catch (err) {
    console.error(err)
  } finally {
    isLoading.value = false
  }
}

// 2. SUBMIT FORM (CREATE / UPDATE)
const submitForm = async () => {
  isSubmitting.value = true
  const token = localStorage.getItem('token')
  const headers = { Authorization: `Bearer ${token}` }

  if (!form.rt) {
    form.rt = localStorage.getItem('rt')
  }

  try {
    if (isEditMode.value) {
      await axios.put(`https://alentest.my.id/laporan/api/auth/users/${editId.value}`, form, { headers })
      Swal.fire('Berhasil', 'Data user diperbarui', 'success')
    } else {
      await axios.post('https://alentest.my.id/laporan/api/auth/users', form, { headers })
      Swal.fire('Berhasil', 'User baru ditambahkan', 'success')
    }
    closeModal()
    fetchUsers()
  } catch (err) {
    Swal.fire('Gagal', err.response?.data?.message || 'Terjadi kesalahan', 'error')
  } finally {
    isSubmitting.value = false
  }
}

// 3. DELETE USER
const deleteUser = async (id) => {
  const result = await Swal.fire({
    title: 'Hapus User?',
    text: "Data yang dihapus tidak dapat dikembalikan.",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#EF4444',
    cancelButtonColor: '#6B7280',
    confirmButtonText: 'Ya, Hapus',
    cancelButtonText: 'Batal',
    reverseButtons: true,
    customClass: {
      popup: 'rounded-xl',
      confirmButton: 'rounded-lg',
      cancelButton: 'rounded-lg'
    }
  })

  if (result.isConfirmed) {
    try {
      const token = localStorage.getItem('token')
      await axios.delete(`https://alentest.my.id/laporan/api/auth/users/${id}`, {
        headers: { Authorization: `Bearer ${token}` }
      })
      Swal.fire('Terhapus!', 'User telah dihapus.', 'success')
      fetchUsers()
    } catch (err) {
      Swal.fire('Gagal', 'Gagal menghapus data', 'error')
    }
  }
}

// --- HELPER FUNCTIONS ---

const handleSearch = () => {
  if (searchTimeout) clearTimeout(searchTimeout)
  searchTimeout = setTimeout(() => {
    pagination.currentPage = 1
    fetchUsers()
  }, 500)
}

const changePage = (p) => {
  pagination.currentPage = p
  fetchUsers()
}

const formatDate = (date) => {
  if (!date) return '-'
  return new Date(date).toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' })
}

const openModal = (mode, data = null) => {
  isEditMode.value = mode === 'edit'
  showModal.value = true
  const defaultRt = localStorage.getItem('rt') || ''

  if (mode === 'edit' && data) {
    editId.value = data.id
    form.username = data.username
    form.phone = data.phone
    form.role = data.role
    form.rt = data.rt || defaultRt
    form.password = ''
  } else {
    form.username = ''
    form.phone = ''
    form.role = 'user'
    form.password = ''
    form.rt = defaultRt
  }
}

const closeModal = () => {
  showModal.value = false
}

onMounted(fetchUsers)
</script>

<style scoped>
/* Modal Transition Animation */
.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-active .transform,
.modal-fade-leave-active .transform {
  transition: transform 0.3s ease;
}

.modal-fade-enter-from .transform,
.modal-fade-leave-to .transform {
  transform: scale(0.95);
}
</style>