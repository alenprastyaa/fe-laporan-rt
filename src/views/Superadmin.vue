<template>
  <AdminLayout>
    <div class="space-y-6">

      <div>
        <h1 class="text-2xl font-bold text-gray-800 dark:text-white tracking-tight">Superadmin</h1>
        <p class="text-sm text-gray-500 dark:text-gray-400 mt-1">Ringkasan dan pengelolaan seluruh RT dalam sistem.
        </p>
      </div>

      <!-- Cards Ringkasan -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <div
          class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm">
          <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total RT</p>
          <h3 class="text-2xl font-bold text-gray-800 dark:text-white mt-1">{{ overview.cards.total_rt }}</h3>
        </div>
        <div
          class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm">
          <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total Warga</p>
          <h3 class="text-2xl font-bold text-gray-800 dark:text-white mt-1">{{ overview.cards.total_warga }}</h3>
        </div>
        <div
          class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm">
          <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total Saldo Semua RT</p>
          <h3 class="text-2xl font-bold text-blue-600 mt-1">{{ formatRupiah(overview.cards.total_saldo) }}</h3>
        </div>
        <div
          class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm">
          <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Pemasukan Tahun Ini</p>
          <h3 class="text-2xl font-bold text-green-600 mt-1">{{ formatRupiah(overview.cards.pemasukan_tahun_ini) }}
          </h3>
        </div>
      </div>

      <!-- Tabel Per RT -->
      <div
        class="bg-white dark:bg-gray-800 rounded-2xl shadow-sm border border-gray-100 dark:border-gray-700 overflow-hidden">
        <div
          class="p-5 border-b border-gray-100 dark:border-gray-700 bg-gray-50/50 dark:bg-gray-800/50 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
          <h2 class="text-base font-semibold text-gray-800 dark:text-white">Ringkasan per RT</h2>
          <button @click="openRtModal"
            class="px-5 py-2.5 bg-gradient-to-r from-emerald-600 to-teal-600 text-white rounded-full shadow-lg shadow-emerald-500/30 hover:shadow-emerald-500/50 hover:scale-[1.02] transition-all duration-300 font-medium text-sm flex items-center gap-2">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Tambah RT Baru
          </button>
        </div>
        <div class="overflow-x-auto">
          <table class="min-w-full align-middle">
            <thead>
              <tr class="bg-gray-50/80 dark:bg-gray-700/30 border-b border-gray-100 dark:border-gray-700">
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  RT</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Admin</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Warga</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Pemasukan</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Pengeluaran</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Saldo</th>
                <th
                  class="px-6 py-3 text-right text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Aksi</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-100 dark:divide-gray-700 bg-white dark:bg-gray-800">
              <tr v-if="isLoadingOverview">
                <td colspan="7" class="px-6 py-10 text-center text-gray-500 text-sm">Memuat data...</td>
              </tr>
              <tr v-else-if="overview.data.length === 0">
                <td colspan="7" class="px-6 py-10 text-center text-gray-500 text-sm">Belum ada RT terdaftar.</td>
              </tr>
              <tr v-else v-for="rtRow in overview.data" :key="rtRow.rt"
                class="hover:bg-blue-50/50 dark:hover:bg-blue-900/10 transition-colors">
                <td class="px-6 py-4 whitespace-nowrap text-sm font-semibold text-gray-800 dark:text-white">RT {{
                  rtRow.rt }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600 dark:text-gray-300">{{
                  rtRow.admin_names || '-' }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600 dark:text-gray-300">{{
                  rtRow.total_warga }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-green-600 font-medium">{{
                  formatRupiah(rtRow.pemasukan) }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-red-600 font-medium">{{
                  formatRupiah(rtRow.pengeluaran) }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-semibold text-blue-600">{{
                  formatRupiah(rtRow.saldo) }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                  <button @click="filterByRt(rtRow.rt)"
                    class="px-3 py-1.5 text-xs font-medium bg-blue-50 text-blue-700 dark:bg-blue-900/30 dark:text-blue-300 rounded-lg hover:bg-blue-100 dark:hover:bg-blue-900/50 transition-colors">
                    Lihat User
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Manajemen User Lintas RT -->
      <div
        class="bg-white dark:bg-gray-800 rounded-2xl shadow-sm border border-gray-100 dark:border-gray-700 overflow-hidden">

        <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 p-5 border-b border-gray-100 dark:border-gray-700 bg-gray-50/50 dark:bg-gray-800/50">
          <div>
            <h2 class="text-base font-semibold text-gray-800 dark:text-white">Manajemen User</h2>
            <p class="text-xs text-gray-500 dark:text-gray-400 mt-0.5">Kelola user, admin, dan superadmin di semua RT.
            </p>
          </div>
          <button @click="openModal('add')"
            class="px-5 py-2.5 bg-gradient-to-r from-blue-600 to-indigo-600 text-white rounded-full shadow-lg shadow-blue-500/30 hover:shadow-blue-500/50 hover:scale-[1.02] transition-all duration-300 font-medium text-sm flex items-center gap-2">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Tambah User
          </button>
        </div>

        <div class="p-5 border-b border-gray-100 dark:border-gray-700 flex flex-col sm:flex-row gap-3">
          <div class="relative w-full sm:w-72">
            <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-gray-400">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
              </svg>
            </span>
            <input v-model="searchQuery" @input="handleSearch" type="text" placeholder="Cari username / HP..."
              class="w-full pl-10 pr-4 py-2.5 bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-600 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 dark:text-white">
          </div>
          <select v-model="selectedRt" @change="refreshUsers"
            class="px-4 py-2.5 border border-gray-200 dark:border-gray-600 rounded-xl text-sm dark:bg-gray-900 dark:text-white">
            <option value="">Semua RT</option>
            <option v-for="rtRow in overview.data" :key="rtRow.rt" :value="rtRow.rt">RT {{ rtRow.rt }}</option>
          </select>
          <select v-model="selectedRole" @change="refreshUsers"
            class="px-4 py-2.5 border border-gray-200 dark:border-gray-600 rounded-xl text-sm dark:bg-gray-900 dark:text-white">
            <option value="">Semua Role</option>
            <option value="superadmin">Superadmin</option>
            <option value="admin">Admin</option>
            <option value="user">Warga</option>
          </select>
        </div>

        <div class="overflow-x-auto">
          <table class="min-w-full align-middle">
            <thead>
              <tr class="bg-gray-50/80 dark:bg-gray-700/30 border-b border-gray-100 dark:border-gray-700">
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  User</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Kontak</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  RT</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Role</th>
                <th
                  class="px-6 py-3 text-left text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Bergabung</th>
                <th
                  class="px-6 py-3 text-right text-xs font-semibold text-gray-500 dark:text-gray-400 uppercase tracking-wider">
                  Aksi</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-100 dark:divide-gray-700 bg-white dark:bg-gray-800">
              <tr v-if="isLoadingUsers">
                <td colspan="6" class="px-6 py-10 text-center text-gray-500 text-sm">Memuat data...</td>
              </tr>
              <tr v-else-if="users.length === 0">
                <td colspan="6" class="px-6 py-10 text-center text-gray-500 text-sm">Data tidak ditemukan.</td>
              </tr>
              <tr v-else v-for="user in users" :key="user.id"
                class="group hover:bg-blue-50/50 dark:hover:bg-blue-900/10 transition-colors">
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center gap-3">
                    <div class="w-9 h-9 rounded-full overflow-hidden ring-2 ring-gray-100 dark:ring-gray-700">
                      <img
                        :src="`https://ui-avatars.com/api/?name=${user.username}&background=random&color=fff&bold=true`"
                        class="w-full h-full object-cover" alt="Avatar" />
                    </div>
                    <div>
                      <div class="text-sm font-semibold text-gray-900 dark:text-white">{{ user.username }}</div>
                      <div class="text-xs text-gray-500 dark:text-gray-400">ID: #{{ user.id }}</div>
                    </div>
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600 dark:text-gray-300">{{ user.phone ||
                  'Belum diisi' }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600 dark:text-gray-300">RT {{ user.rt }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <span :class="[
                    'px-3 py-1 rounded-full text-xs font-semibold border capitalize',
                    roleBadgeClass(user.role)
                  ]">
                    {{ user.role }}
                  </span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">{{
                  formatDate(user.created_at) }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                  <div class="flex items-center justify-end gap-2">
                    <button @click="openModal('edit', user)"
                      class="p-2 bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 text-gray-600 dark:text-gray-300 rounded-lg hover:border-blue-500 hover:text-blue-600 transition-all"
                      title="Edit">
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                          d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z">
                        </path>
                      </svg>
                    </button>
                    <button @click="deleteUser(user.id)"
                      class="p-2 bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 text-gray-600 dark:text-gray-300 rounded-lg hover:border-red-500 hover:text-red-600 transition-all"
                      title="Hapus">
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
            Halaman {{ pagination.currentPage }} dari {{ pagination.totalPage }} ({{ pagination.totalData }} data)
          </span>
          <div class="flex items-center gap-2">
            <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage <= 1"
              class="px-4 py-2 text-sm font-medium bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors text-gray-700 dark:text-white">
              Previous
            </button>
            <button @click="changePage(pagination.currentPage + 1)"
              :disabled="pagination.currentPage >= pagination.totalPage"
              class="px-4 py-2 text-sm font-medium bg-white dark:bg-gray-700 border border-gray-200 dark:border-gray-600 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors text-gray-700 dark:text-white">
              Next
            </button>
          </div>
        </div>
      </div>

      <!-- Modal Tambah / Edit User -->
      <Transition name="modal-fade">
        <div v-if="showModal" class="fixed inset-0 z-50 flex items-center justify-center p-4">
          <div class="absolute inset-0 bg-gray-900/60 backdrop-blur-sm" @click="closeModal"></div>

          <div
            class="relative bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl border border-gray-100 dark:border-gray-700 overflow-hidden">
            <div
              class="px-6 py-5 border-b border-gray-100 dark:border-gray-700 flex justify-between items-center bg-gray-50/50 dark:bg-gray-800/50">
              <h3 class="text-lg font-bold text-gray-800 dark:text-white">{{ isEditMode ? 'Edit User' : 'Tambah User Baru' }}</h3>
              <button @click="closeModal"
                class="p-1 rounded-full text-gray-400 hover:text-gray-600 hover:bg-gray-100 dark:hover:bg-gray-700">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>

            <form @submit.prevent="submitForm" class="p-6 space-y-4">
              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Username</label>
                <input v-model="form.username" type="text"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none text-sm dark:text-white"
                  placeholder="Masukkan username" required>
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Nomor HP</label>
                <input v-model="form.phone" type="text"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none text-sm dark:text-white"
                  placeholder="0812...">
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">RT</label>
                <select v-model="form.rt"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none text-sm dark:text-white"
                  required>
                  <option value="" disabled>-- Pilih RT --</option>
                  <option v-for="rtRow in overview.data" :key="rtRow.rt" :value="rtRow.rt">RT {{ rtRow.rt }}</option>
                </select>
                <p class="text-xs text-gray-400 mt-1">RT belum ada di daftar? Buat dulu lewat tombol "Tambah RT Baru".
                </p>
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Role Akses</label>
                <select v-model="form.role"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none text-sm dark:text-white">
                  <option value="user">Warga</option>
                  <option value="admin">Admin RT</option>
                  <option value="superadmin">Superadmin</option>
                </select>
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">
                  Password {{ isEditMode ? '(Opsional)' : '' }}
                </label>
                <input v-model="form.password" type="password"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none text-sm dark:text-white"
                  placeholder="••••••••" :required="!isEditMode">
              </div>

              <div class="pt-2 flex gap-3">
                <button type="button" @click="closeModal"
                  class="flex-1 px-4 py-2.5 border border-gray-200 dark:border-gray-600 rounded-xl text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-700 font-medium text-sm">
                  Batal
                </button>
                <button type="submit" :disabled="isSubmitting"
                  class="flex-1 px-4 py-2.5 bg-gradient-to-r from-blue-600 to-indigo-600 text-white rounded-xl hover:shadow-lg hover:shadow-blue-500/30 font-medium text-sm disabled:opacity-70">
                  {{ isSubmitting ? 'Menyimpan...' : 'Simpan' }}
                </button>
              </div>
            </form>
          </div>
        </div>
      </Transition>

      <!-- Modal Tambah RT Baru -->
      <Transition name="modal-fade">
        <div v-if="showRtModal" class="fixed inset-0 z-50 flex items-center justify-center p-4">
          <div class="absolute inset-0 bg-gray-900/60 backdrop-blur-sm" @click="closeRtModal"></div>

          <div
            class="relative bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl border border-gray-100 dark:border-gray-700 overflow-hidden">
            <div
              class="px-6 py-5 border-b border-gray-100 dark:border-gray-700 flex justify-between items-center bg-gray-50/50 dark:bg-gray-800/50">
              <div>
                <h3 class="text-lg font-bold text-gray-800 dark:text-white">Tambah RT Baru</h3>
                <p class="text-xs text-gray-500 mt-0.5">RT baru dibuat bersamaan dengan akun admin pertamanya.</p>
              </div>
              <button @click="closeRtModal"
                class="p-1 rounded-full text-gray-400 hover:text-gray-600 hover:bg-gray-100 dark:hover:bg-gray-700">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>

            <form @submit.prevent="submitRt" class="p-6 space-y-4">
              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Nomor RT</label>
                <input v-model="rtForm.rt" type="text"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 outline-none text-sm dark:text-white"
                  placeholder="Contoh: 005" required>
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Username Admin</label>
                <input v-model="rtForm.username" type="text"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 outline-none text-sm dark:text-white"
                  placeholder="Username untuk admin RT ini" required>
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Nomor HP</label>
                <input v-model="rtForm.phone" type="text"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 outline-none text-sm dark:text-white"
                  placeholder="0812...">
              </div>

              <div>
                <label class="block text-xs font-semibold text-gray-500 dark:text-gray-400 mb-1.5 uppercase tracking-wide">Password Admin</label>
                <input v-model="rtForm.password" type="password"
                  class="w-full px-4 py-2.5 bg-gray-50 dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 outline-none text-sm dark:text-white"
                  placeholder="••••••••" required>
              </div>

              <div class="pt-2 flex gap-3">
                <button type="button" @click="closeRtModal"
                  class="flex-1 px-4 py-2.5 border border-gray-200 dark:border-gray-600 rounded-xl text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-700 font-medium text-sm">
                  Batal
                </button>
                <button type="submit" :disabled="isSubmittingRt"
                  class="flex-1 px-4 py-2.5 bg-gradient-to-r from-emerald-600 to-teal-600 text-white rounded-xl hover:shadow-lg hover:shadow-emerald-500/30 font-medium text-sm disabled:opacity-70">
                  {{ isSubmittingRt ? 'Menyimpan...' : 'Buat RT' }}
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

const API_BASE = import.meta.env.DEV ? 'http://localhost:6500/api' : 'https://alentest.my.id/laporan/api'

const overview = reactive({
  cards: { total_rt: 0, total_warga: 0, total_saldo: 0, pemasukan_tahun_ini: 0 },
  data: []
})
const isLoadingOverview = ref(false)

const users = ref([])
const isLoadingUsers = ref(false)
const searchQuery = ref('')
const selectedRt = ref('')
const selectedRole = ref('')
let searchTimeout = null

const pagination = reactive({ currentPage: 1, limit: 8, totalData: 0, totalPage: 1 })

const showModal = ref(false)
const isEditMode = ref(false)
const isSubmitting = ref(false)
const editId = ref(null)

const form = reactive({ username: '', phone: '', rt: '', role: 'user', password: '' })

const showRtModal = ref(false)
const isSubmittingRt = ref(false)
const rtForm = reactive({ rt: '', username: '', phone: '', password: '' })

const authHeaders = () => ({ Authorization: `Bearer ${localStorage.getItem('token')}` })

const fetchOverview = async () => {
  isLoadingOverview.value = true
  try {
    const res = await axios.get(`${API_BASE}/dashboard/superadmin-overview`, { headers: authHeaders() })
    if (res.data.status === 'success') {
      overview.cards = res.data.cards
      overview.data = res.data.data
    }
  } catch (err) {
    console.error(err)
  } finally {
    isLoadingOverview.value = false
  }
}

const fetchUsers = async () => {
  isLoadingUsers.value = true
  try {
    const res = await axios.get(`${API_BASE}/auth/users`, {
      headers: authHeaders(),
      params: {
        page: pagination.currentPage,
        limit: pagination.limit,
        search: searchQuery.value,
        role: selectedRole.value,
        rt: selectedRt.value
      }
    })
    if (res.data.status === 'success') {
      users.value = res.data.data
      pagination.currentPage = res.data.pagination.currentPage
      pagination.totalPage = res.data.pagination.totalPage
      pagination.totalData = res.data.pagination.totalData
    }
  } catch (err) {
    console.error(err)
  } finally {
    isLoadingUsers.value = false
  }
}

const refreshUsers = () => {
  pagination.currentPage = 1
  fetchUsers()
}

const filterByRt = (rt) => {
  selectedRt.value = rt
  refreshUsers()
}

const handleSearch = () => {
  if (searchTimeout) clearTimeout(searchTimeout)
  searchTimeout = setTimeout(refreshUsers, 500)
}

const changePage = (p) => {
  if (p < 1 || p > pagination.totalPage) return
  pagination.currentPage = p
  fetchUsers()
}

const submitForm = async () => {
  isSubmitting.value = true
  try {
    if (isEditMode.value) {
      await axios.put(`${API_BASE}/auth/users/${editId.value}`, form, { headers: authHeaders() })
      Swal.fire('Berhasil', 'Data user diperbarui', 'success')
    } else {
      await axios.post(`${API_BASE}/auth/users`, form, { headers: authHeaders() })
      Swal.fire('Berhasil', 'User baru ditambahkan', 'success')
    }
    closeModal()
    fetchUsers()
    fetchOverview()
  } catch (err) {
    Swal.fire('Gagal', err.response?.data?.message || 'Terjadi kesalahan', 'error')
  } finally {
    isSubmitting.value = false
  }
}

const deleteUser = async (id) => {
  const result = await Swal.fire({
    title: 'Hapus User?',
    text: 'Data yang dihapus tidak dapat dikembalikan.',
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#EF4444',
    cancelButtonColor: '#6B7280',
    confirmButtonText: 'Ya, Hapus',
    cancelButtonText: 'Batal',
    reverseButtons: true
  })
  if (!result.isConfirmed) return

  try {
    await axios.delete(`${API_BASE}/auth/users/${id}`, { headers: authHeaders() })
    Swal.fire('Terhapus!', 'User telah dihapus.', 'success')
    fetchUsers()
    fetchOverview()
  } catch (err) {
    Swal.fire('Gagal', err.response?.data?.message || 'Gagal menghapus data', 'error')
  }
}

const openModal = (mode, data = null) => {
  isEditMode.value = mode === 'edit'
  showModal.value = true

  if (mode === 'edit' && data) {
    editId.value = data.id
    form.username = data.username
    form.phone = data.phone
    form.rt = data.rt
    form.role = data.role
    form.password = ''
  } else {
    editId.value = null
    form.username = ''
    form.phone = ''
    form.rt = selectedRt.value || ''
    form.role = 'user'
    form.password = ''
  }
}

const closeModal = () => {
  showModal.value = false
}

const openRtModal = () => {
  rtForm.rt = ''
  rtForm.username = ''
  rtForm.phone = ''
  rtForm.password = ''
  showRtModal.value = true
}

const closeRtModal = () => {
  showRtModal.value = false
}

const submitRt = async () => {
  const rtExists = overview.data.some(row => row.rt === rtForm.rt)
  if (rtExists) {
    Swal.fire('Gagal', `RT ${rtForm.rt} sudah terdaftar`, 'error')
    return
  }

  isSubmittingRt.value = true
  try {
    await axios.post(`${API_BASE}/auth/users`, {
      username: rtForm.username,
      phone: rtForm.phone,
      password: rtForm.password,
      role: 'admin',
      rt: rtForm.rt
    }, { headers: authHeaders() })
    Swal.fire('Berhasil', `RT ${rtForm.rt} berhasil dibuat`, 'success')
    closeRtModal()
    fetchOverview()
    fetchUsers()
  } catch (err) {
    Swal.fire('Gagal', err.response?.data?.message || 'Terjadi kesalahan', 'error')
  } finally {
    isSubmittingRt.value = false
  }
}

const roleBadgeClass = (role) => {
  if (role === 'superadmin') return 'bg-amber-50 text-amber-700 border-amber-100 dark:bg-amber-900/30 dark:text-amber-300 dark:border-amber-800'
  if (role === 'admin') return 'bg-purple-50 text-purple-700 border-purple-100 dark:bg-purple-900/30 dark:text-purple-300 dark:border-purple-800'
  return 'bg-blue-50 text-blue-700 border-blue-100 dark:bg-blue-900/30 dark:text-blue-300 dark:border-blue-800'
}

const formatDate = (date) => {
  if (!date) return '-'
  return new Date(date).toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' })
}

const formatRupiah = (val) =>
  new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(val || 0)

onMounted(() => {
  fetchOverview()
  fetchUsers()
})
</script>

<style scoped>
.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-active .relative,
.modal-fade-leave-active .relative {
  transition: transform 0.3s ease;
}

.modal-fade-enter-from .relative,
.modal-fade-leave-to .relative {
  transform: scale(0.95);
}
</style>
