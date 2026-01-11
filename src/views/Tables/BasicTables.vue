<template>
  <AdminLayout>
    <div class="space-y-4">

      <div
        class="flex flex-col sm:flex-row gap-4 justify-between items-center bg-white dark:bg-gray-900 p-4 rounded-xl border border-gray-200 dark:border-gray-800">

        <div class="relative w-full sm:w-64">
          <span class="absolute inset-y-0 left-0 flex items-center pl-3">
            <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
            </svg>
          </span>
          <input v-model="searchQuery" @input="handleSearch" type="text" placeholder="Cari Username / HP..."
            class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
        </div>

        <div class="flex items-center gap-3 w-full sm:w-auto">
          <!-- <select v-model="selectedRole" @change="fetchUsers"
            class="px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
            <option value="">Semua Role</option>
            <option value="admin">Admin</option>
            <option value="user">User</option>
          </select> -->

          <button @click="openModal('add')"
            class="flex items-center gap-2 bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-colors text-sm font-medium">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Tambah User
          </button>
        </div>
      </div>

      <div class="overflow-hidden rounded-xl border border-gray-200 bg-white dark:border-gray-800 dark:bg-white/[0.03]">
        <div class="max-w-full overflow-x-auto custom-scrollbar">
          <table class="min-w-full">
            <thead>
              <tr class="border-b border-gray-200 dark:border-gray-700 bg-gray-50 dark:bg-gray-800/50">
                <th class="px-5 py-3 text-left sm:px-6 font-medium text-gray-500 text-xs uppercase">User</th>
                <th class="px-5 py-3 text-left sm:px-6 font-medium text-gray-500 text-xs uppercase">Kontak</th>
                <th class="px-5 py-3 text-left sm:px-6 font-medium text-gray-500 text-xs uppercase">Role</th>
                <th class="px-5 py-3 text-left sm:px-6 font-medium text-gray-500 text-xs uppercase">Bergabung</th>
                <th class="px-5 py-3 text-right sm:px-6 font-medium text-gray-500 text-xs uppercase">Aksi</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
              <tr v-if="isLoading">
                <td colspan="5" class="px-5 py-8 text-center text-gray-500">Memuat data...</td>
              </tr>
              <tr v-else-if="users.length === 0">
                <td colspan="5" class="px-5 py-8 text-center text-gray-500">Tidak ada data ditemukan.</td>
              </tr>

              <tr v-else v-for="user in users" :key="user.id"
                class="hover:bg-gray-50 dark:hover:bg-white/[0.02] transition-colors">
                <td class="px-5 py-4 sm:px-6">
                  <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-full overflow-hidden bg-gray-200">
                      <img :src="`https://ui-avatars.com/api/?name=${user.username}&background=random`" alt="Avatar" />
                    </div>
                    <div>
                      <span class="block font-medium text-gray-800 dark:text-white/90 text-sm">{{ user.username
                      }}</span>
                    </div>
                  </div>
                </td>
                <td class="px-5 py-4 sm:px-6 text-sm text-gray-600 dark:text-gray-400">{{ user.phone || '-' }}</td>
                <td class="px-5 py-4 sm:px-6">
                  <span
                    :class="['rounded-full px-2 py-1 text-xs font-medium capitalize', user.role === 'admin' ? 'bg-purple-100 text-purple-700' : 'bg-blue-100 text-blue-700']">
                    {{ user.role }}
                  </span>
                </td>
                <td class="px-5 py-4 sm:px-6 text-sm text-gray-500">{{ formatDate(user.created_at) }}</td>
                <td class="px-5 py-4 sm:px-6 text-right">
                  <div class="flex items-center justify-end gap-2">
                    <button @click="openModal('edit', user)"
                      class="p-2 text-blue-600 hover:bg-blue-50 rounded-lg transition" title="Edit">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                          d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z">
                        </path>
                      </svg>
                    </button>
                    <button @click="deleteUser(user.id)" class="p-2 text-red-600 hover:bg-red-50 rounded-lg transition"
                      title="Hapus">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
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

        <div class="border-t border-gray-200 dark:border-gray-700 px-5 py-4 flex items-center justify-between">
          <span class="text-sm text-gray-500">Hal {{ pagination.currentPage }} dari {{ pagination.totalPage }}</span>
          <div class="flex gap-2">
            <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage <= 1"
              class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Prev</button>
            <button @click="changePage(pagination.currentPage + 1)"
              :disabled="pagination.currentPage >= pagination.totalPage"
              class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Next</button>
          </div>
        </div>
      </div>

      <div v-if="showModal"
        class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 p-4 backdrop-blur-sm">
        <div
          class="bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl overflow-hidden animate-fade-in-up">
          <div class="px-6 py-4 border-b border-gray-100 dark:border-gray-700 flex justify-between items-center">
            <h3 class="text-lg font-bold text-gray-800 dark:text-white">{{ isEditMode ? 'Edit User' : 'Tambah User Baru'
            }}</h3>
            <button @click="closeModal" class="text-gray-400 hover:text-gray-600"><svg class="w-6 h-6" fill="none"
                stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg></button>
          </div>

          <form @submit.prevent="submitForm" class="p-6 space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Username</label>
              <input v-model="form.username" type="text"
                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none dark:bg-gray-900 dark:border-gray-600 dark:text-white"
                required>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Nomor HP</label>
              <input v-model="form.phone" type="text"
                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none dark:bg-gray-900 dark:border-gray-600 dark:text-white">
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Role</label>
              <select v-model="form.role"
                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none dark:bg-gray-900 dark:border-gray-600 dark:text-white">
                <option value="user">warga</option>
                <!-- <option value="admin">Admin</option> -->
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Password {{ isEditMode ? '(Kosongkan jika tidak diubah)' : '' }}
              </label>
              <input v-model="form.password" type="password"
                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none dark:bg-gray-900 dark:border-gray-600 dark:text-white"
                :required="!isEditMode">
            </div>

            <div class="pt-2 flex gap-3">
              <button type="button" @click="closeModal"
                class="flex-1 px-4 py-2 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-50 font-medium dark:text-white dark:hover:text-black">Batal</button>
              <button type="submit"
                class="flex-1 px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 font-medium shadow-lg shadow-blue-500/30">
                {{ isSubmitting ? 'Menyimpan...' : 'Simpan' }}
              </button>
            </div>
          </form>
        </div>
      </div>

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

// Form Data - Update 1: Tambahkan properti rt
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

  // Pastikan RT terisi sebelum kirim (jaga-jaga jika terhapus)
  if (!form.rt) {
    form.rt = localStorage.getItem('rt')
  }

  try {
    if (isEditMode.value) {
      // Logic UPDATE
      await axios.put(`https://alentest.my.id/laporan/api/auth/users/${editId.value}`, form, { headers })
      Swal.fire('Berhasil', 'Data user diperbarui', 'success')
    } else {
      // Logic CREATE
      await axios.post('https://alentest.my.id/laporan/api/auth/users', form, { headers })
      Swal.fire('Berhasil', 'User baru ditambahkan', 'success')
    }
    closeModal()
    fetchUsers() // Refresh Table
  } catch (err) {
    Swal.fire('Gagal', err.response?.data?.message || 'Terjadi kesalahan', 'error')
  } finally {
    isSubmitting.value = false
  }
}

// 3. DELETE USER
const deleteUser = async (id) => {
  const result = await Swal.fire({
    title: 'Yakin hapus user ini?',
    text: "Data tidak bisa dikembalikan!",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#d33',
    cancelButtonColor: '#3085d6',
    confirmButtonText: 'Ya, Hapus!',
    cancelButtonText: 'Batal'
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
  return new Date(date).toLocaleDateString('id-ID', { day: 'numeric', month: 'short', year: 'numeric' })
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

// Init
onMounted(fetchUsers)
</script>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  height: 6px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 10px;
}

.dark .custom-scrollbar::-webkit-scrollbar-thumb {
  background: #475569;
}

@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(10px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in-up {
  animation: fade-in-up 0.3s ease-out;
}
</style>