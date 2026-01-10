<template>
    <AdminLayout>
        <div class="space-y-2">
            <!-- <div>
                <h2 class="text-sm font-bold text-gray-800 dark:text-white">Laporan Kas RT</h2>
                <p class="text-sm text-gray-500 dark:text-gray-400">Periode: {{ monthNames[filters.bulan - 1] }} {{
                    filters.tahun }}</p>
            </div> -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div
                    class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total Pemasukan
                            (Semua)</p>
                        <h3 class="text-xl font-bold text-green-600 mt-1">{{ formatRupiah(globalStats.pemasukan) }}</h3>
                    </div>

                </div>
                <div
                    class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total Pengeluaran
                            (Semua)</p>
                        <h3 class="text-xl font-bold text-red-600 mt-1">{{ formatRupiah(globalStats.pengeluaran) }}</h3>
                    </div>

                </div>
                <div
                    class="bg-white dark:bg-gray-800 p-5 rounded-xl border border-gray-200 dark:border-gray-700 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Saldo Akhir</p>
                        <h3 class="text-2xl font-bold text-blue-600 mt-1">{{ formatRupiah(globalStats.saldo) }}</h3>
                    </div>

                </div>
            </div>

            <div class="flex flex-col sm:flex-row justify-between items-center gap-4">


                <div class="bg-gray-100 dark:bg-gray-800 p-1 rounded-lg flex gap-1">
                    <button @click="changeMainTab('masuk')"
                        :class="['px-6 py-2 rounded-md text-sm font-medium transition-all', activeMainTab === 'masuk' ? 'bg-white dark:bg-gray-700 shadow text-blue-600 dark:text-blue-400' : 'text-gray-500 hover:text-gray-700 dark:text-gray-400']">
                        Kas Masuk
                    </button>
                    <button @click="changeMainTab('keluar')"
                        :class="['px-6 py-2 rounded-md text-sm font-medium transition-all', activeMainTab === 'keluar' ? 'bg-white dark:bg-gray-700 shadow text-red-600 dark:text-red-400' : 'text-gray-500 hover:text-gray-700 dark:text-gray-400']">
                        Kas Keluar
                    </button>
                </div>
            </div>

            <div
                class="bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-800 rounded-xl p-5 shadow-sm min-h-[500px]">

                <div v-if="activeMainTab === 'masuk'" class="space-y-6">

                    <div class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4">
                        <div class="flex flex-col sm:flex-row gap-3 w-full md:w-auto">
                            <div class="relative w-full sm:w-48">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3">
                                    <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor"
                                        viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                                    </svg>
                                </span>
                                <input v-model="searchQuery" @input="handleSearch" type="text"
                                    placeholder="Cari Warga..."
                                    class="w-full pl-9 pr-4 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                            </div>
                            <div class="flex gap-2 w-full md:w-auto">
                                <select v-model="filters.bulan" @change="refreshData"
                                    class="block w-full md:w-28 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                    <option v-for="(m, i) in monthNames" :key="i" :value="i + 1">{{ m }}</option>
                                </select>
                                <select v-model="filters.tahun" @change="refreshData"
                                    class="block w-full md:w-24 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                    <option v-for="y in [2023, 2024, 2025, 2026]" :key="y" :value="y">{{ y }}</option>
                                </select>
                            </div>
                        </div>

                        <div class="flex flex-col sm:flex-row gap-3 w-full md:w-auto items-stretch">
                            <div class="flex bg-gray-100 dark:bg-gray-800 p-1 rounded-lg">
                                <button @click="changeSubTab('sudah')"
                                    :class="['px-3 py-1.5 text-xs font-medium rounded transition', activeSubTab === 'sudah' ? 'bg-green-100 text-green-700 dark:bg-green-900 dark:text-green-300' : 'text-gray-500']">Lunas</button>
                                <button @click="changeSubTab('belum')"
                                    :class="['px-3 py-1.5 text-xs font-medium rounded transition', activeSubTab === 'belum' ? 'bg-red-100 text-red-700 dark:bg-red-900 dark:text-red-300' : 'text-gray-500']">Belum</button>
                            </div>
                            <button v-show="role === 'admin'" @click="openModalMasuk('add')"
                                class="flex items-center justify-center gap-2 bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition whitespace-nowrap">
                                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                        d="M12 4v16m8-8H4"></path>
                                </svg>
                                Input Manual
                            </button>
                        </div>
                    </div>

                    <div v-if="activeSubTab === 'sudah'"
                        class="flex justify-between items-center bg-gray-50 dark:bg-gray-800/50 p-4 rounded-lg border border-gray-100 dark:border-gray-700">
                        <span class="text-sm font-medium text-gray-500">Total Pemasukan Bulan Ini:</span>
                        <span class="text-lg font-bold text-green-600">{{ formatRupiah(totalMasukSummary) }}</span>
                    </div>
                    <p class="text-sm text-gray-500 dark:text-gray-400">Periode: {{ monthNames[filters.bulan - 1] }} {{
                        filters.tahun }}</p>
                    <div v-if="activeSubTab === 'belum'"
                        class="mb-4 flex items-center justify-between bg-blue-50 p-3 rounded-lg border border-blue-100 dark:bg-blue-900/20 dark:border-blue-800">
                        <div class="flex items-center gap-3 pl-2" v-show="role === 'admin'">
                            <input type="checkbox" v-model="isSelectAll" @change="toggleSelectAll"
                                class="w-4 h-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500">
                            <span class="text-sm font-medium text-blue-700 dark:text-blue-300">Pilih Semua di Halaman
                                Ini</span>
                        </div>
                        <button v-if="selectedUsers.length > 0" @click="openModalBulk"
                            class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-md text-sm font-bold shadow-sm transition-all whitespace-nowrap">
                            Bayar {{ selectedUsers.length }} Warga
                        </button>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr
                                    class="border-b border-gray-100 dark:border-gray-800 text-gray-500 text-xs uppercase bg-gray-50 dark:bg-gray-800/50">
                                    <th v-show="role === 'admin'" v-if="activeSubTab === 'belum'" class="p-4 w-10"></th>
                                    <th class="p-4">Nama Warga</th>
                                    <th class="p-4">Periode</th>
                                    <th class="p-4" v-if="activeSubTab === 'sudah'">Wajib</th>
                                    <th class="p-4" v-if="activeSubTab === 'sudah'">Sukarela</th>
                                    <th class="p-4 text-right">Status</th>
                                    <th v-show="role === 'admin'" class="p-4 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-100 dark:divide-gray-800">
                                <tr v-if="loading">
                                    <td :colspan="activeSubTab === 'belum' ? 7 : 6"
                                        class="p-6 text-center text-gray-500">Memuat data...</td>
                                </tr>
                                <tr v-else-if="kasMasukList.length === 0">
                                    <td :colspan="activeSubTab === 'belum' ? 7 : 6"
                                        class="p-6 text-center text-gray-500">Tidak ada data ditemukan.</td>
                                </tr>
                                <tr v-else v-for="item in kasMasukList" :key="item.id || item.user_id"
                                    class="hover:bg-gray-50 dark:hover:bg-gray-800/30">
                                    <td v-show="role === 'admin'" v-if="activeSubTab === 'belum'" class="p-4">
                                        <input type="checkbox" :value="item.id" v-model="selectedUsers"
                                            class="w-4 h-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500">
                                    </td>
                                    <td class="p-4">
                                        <div class="font-medium text-gray-800 dark:text-white">{{ item.username }}</div>
                                        <div class="text-xs text-gray-500">{{ item.phone || '-' }}</div>
                                    </td>
                                    <td class="p-4 text-sm text-gray-600 dark:text-gray-400">{{ monthNames[filters.bulan
                                        - 1] }} {{ filters.tahun }}</td>
                                    <template v-if="activeSubTab === 'sudah'">
                                        <td class="p-4 text-sm font-medium text-green-600">{{
                                            formatRupiah(item.iuran_wajib) }}</td>
                                        <td class="p-4 text-sm font-medium text-blue-600">{{
                                            formatRupiah(item.iuran_bebas) }}</td>
                                    </template>
                                    <td class="p-4 text-right">
                                        <span v-if="activeSubTab === 'sudah'"
                                            class="px-2 py-1 rounded-full text-xs font-bold bg-green-100 text-green-700">LUNAS</span>
                                        <span v-else
                                            class="px-2 py-1 rounded-full text-xs font-bold bg-red-100 text-red-700">Belum
                                            bayar
                                        </span>
                                    </td>
                                    <td class="p-4 flex justify-center gap-2" v-show="role === 'admin'">
                                        <template v-if="activeSubTab === 'sudah'">
                                            <button @click="openModalMasuk('edit', item)"
                                                class="text-blue-500 hover:bg-blue-50 p-1.5 rounded"><svg
                                                    class="w-4 h-4" fill="none" stroke="currentColor"
                                                    viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round"
                                                        stroke-width="2"
                                                        d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z">
                                                    </path>
                                                </svg></button>
                                            <button @click="deleteItem('masuk', item.id)"
                                                class="text-red-500 hover:bg-red-50 p-1.5 rounded"><svg class="w-4 h-4"
                                                    fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round"
                                                        stroke-width="2"
                                                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                                                    </path>
                                                </svg></button>
                                        </template>
                                        <template v-else>
                                            <button @click="openModalMasuk('pay', item)"
                                                class="px-3 py-1 bg-green-600 text-white text-xs rounded hover:bg-green-700">Bayar</button>
                                        </template>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <div v-if="activeMainTab === 'keluar'" class="space-y-6">

                    <div class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4">
                        <div class="flex flex-col sm:flex-row gap-3 w-full md:w-auto">
                            <div class="relative w-full sm:w-48">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3">
                                    <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor"
                                        viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                                    </svg>
                                </span>
                                <input v-model="searchQuery" @input="handleSearch" type="text"
                                    placeholder="Cari Pengeluaran..."
                                    class="w-full pl-9 pr-4 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                            </div>
                            <div class="flex gap-2 w-full md:w-auto">
                                <select v-model="filters.bulan" @change="refreshData"
                                    class="block w-full md:w-28 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                    <option v-for="(m, i) in monthNames" :key="i" :value="i + 1">{{ m }}</option>
                                </select>
                                <select v-model="filters.tahun" @change="refreshData"
                                    class="block w-full md:w-24 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                    <option v-for="y in [2023, 2024, 2025, 2026]" :key="y" :value="y">{{ y }}</option>
                                </select>
                            </div>
                        </div>

                        <button v-show="role === 'admin'" @click="openModalKeluar('add')"
                            class="flex items-center gap-2 bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm font-medium whitespace-nowrap">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 4v16m8-8H4"></path>
                            </svg>
                            Catat Pengeluaran
                        </button>
                    </div>

                    <div
                        class="flex justify-between items-center bg-gray-50 dark:bg-gray-800/50 p-4 rounded-lg border border-gray-100 dark:border-gray-700">
                        <span class="text-sm font-medium text-gray-500">Total Pengeluaran Bulan Ini:</span>
                        <span class="text-lg font-bold text-red-600">{{ formatRupiah(totalKeluarSummary) }}</span>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr
                                    class="border-b border-gray-100 dark:border-gray-800 text-gray-500 text-xs uppercase bg-gray-50 dark:bg-gray-800/50">
                                    <th class="p-4">Tanggal</th>
                                    <th class="p-4">Keperluan</th>
                                    <th class="p-4">Nominal</th>
                                    <th v-show="role === 'admin'" class="p-4 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-100 dark:divide-gray-800">
                                <tr v-if="loading">
                                    <td colspan="4" class="p-6 text-center text-gray-500">Memuat data...</td>
                                </tr>
                                <tr v-else-if="kasKeluarList.length === 0">
                                    <td colspan="4" class="p-6 text-center text-gray-500">Belum ada data pengeluaran.
                                    </td>
                                </tr>
                                <tr v-else v-for="item in kasKeluarList" :key="item.id"
                                    class="hover:bg-gray-50 dark:hover:bg-gray-800/30">
                                    <td class="p-4 text-sm text-gray-600 dark:text-gray-400">{{ new
                                        Date(item.tanggal_pengeluaran).toLocaleDateString('id-ID') }}</td>
                                    <td class="p-4 text-sm font-medium text-gray-800 dark:text-white">{{ item.keperluan
                                        }}</td>
                                    <td class="p-4 text-sm font-bold text-red-600">{{
                                        formatRupiah(item.total_pengeluaran) }}</td>
                                    <td v-show="role === 'admin'" class="p-4 flex justify-center gap-2">
                                        <button @click="openModalKeluar('edit', item)"
                                            class="text-blue-500 hover:bg-blue-50 p-1.5 rounded"><svg class="w-4 h-4"
                                                fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z">
                                                </path>
                                            </svg></button>
                                        <button @click="deleteItem('keluar', item.id)"
                                            class="text-red-500 hover:bg-red-50 p-1.5 rounded"><svg class="w-4 h-4"
                                                fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                                                </path>
                                            </svg></button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <div class="flex justify-between items-center border-t border-gray-200 dark:border-gray-700 pt-4 mt-4">
                    <div class="text-sm text-gray-500">
                        Halaman {{ pagination.currentPage }} dari {{ pagination.totalPage }} (Total {{
                            pagination.totalData }} Data)
                    </div>
                    <div class="flex gap-2">
                        <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage <= 1"
                            class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Prev</button>
                        <button @click="changePage(pagination.currentPage + 1)"
                            :disabled="pagination.currentPage >= pagination.totalPage"
                            class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Next</button>
                    </div>
                </div>

            </div>

            <div v-if="showModalMasuk"
                class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 p-4 backdrop-blur-sm">
                <div class="bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl p-6">
                    <h3 class="text-lg font-bold mb-4 text-gray-800 dark:text-white">{{ selectedUsers.length > 0 &&
                        !formMasuk.id ?
                        'Pembayaran Massal' : (formMasuk.id ? 'Edit Pembayaran' : 'Input Pembayaran') }}</h3>
                    <form @submit.prevent="submitMasuk" class="space-y-4">
                        <div v-if="selectedUsers.length === 0 && !formMasuk.id">
                            <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Pilih Warga</label>
                            <select v-model="formMasuk.user_id"
                                class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                                required>
                                <option value="" disabled>-- Pilih Warga --</option>
                                <option v-for="u in userList" :key="u.id" :value="u.id">{{ u.username }}</option>
                            </select>
                        </div>
                        <div v-else-if="selectedUsers.length > 0 && !formMasuk.id"
                            class="bg-blue-50 text-blue-700 p-3 rounded-lg text-sm border border-blue-100 flex items-center gap-2">
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                            </svg>
                            <span>Membayar untuk <strong>{{ selectedUsers.length }} warga</strong> terpilih.</span>
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Iuran Wajib</label>
                                <input v-model="formMasuk.iuran_wajib" type="number"
                                    class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white">
                            </div>
                            <div>
                                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Sukarela</label>
                                <input v-model="formMasuk.iuran_bebas" type="number"
                                    class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white">
                            </div>
                        </div>
                        <div class="text-sm text-gray-500">Periode: {{ monthNames[filters.bulan - 1] }} {{ filters.tahun
                            }}</div>
                        <div class="flex gap-3 pt-2">
                            <button type="button" @click="showModalMasuk = false"
                                class="flex-1 py-2 border rounded-lg text-gray-600 hover:bg-gray-50">Batal</button>
                            <button type="submit"
                                class="flex-1 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 font-medium">Simpan</button>
                        </div>
                    </form>
                </div>
            </div>

            <div v-if="showModalKeluar"
                class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 p-4 backdrop-blur-sm">
                <div class="bg-white dark:bg-gray-800 rounded-2xl w-full max-w-md shadow-2xl p-6">
                    <h3 class="text-lg font-bold mb-4 text-gray-800 dark:text-white">{{ formKeluar.id ?
                        'Edit Pengeluaran'
                        : 'Catat Pengeluaran' }}</h3>
                    <form @submit.prevent="submitKeluar" class="space-y-4">
                        <div>
                            <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Keperluan</label>
                            <input v-model="formKeluar.keperluan" type="text"
                                class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                                required placeholder="Contoh: Beli Lampu Jalan">
                        </div>
                        <div>
                            <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Tanggal</label>
                            <input v-model="formKeluar.tanggal_pengeluaran" type="date"
                                class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                                required>
                        </div>
                        <div>
                            <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Total Biaya (Rp)</label>
                            <input v-model="formKeluar.total_pengeluaran" type="number"
                                class="w-full mt-1 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                                required>
                        </div>
                        <div class="flex gap-3 pt-2">
                            <button type="button" @click="showModalKeluar = false"
                                class="flex-1 py-2 border rounded-lg text-gray-600 hover:bg-gray-50">Batal</button>
                            <button type="submit"
                                class="flex-1 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 font-medium">Simpan</button>
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

// --- STATE ---
const activeMainTab = ref('masuk')
const activeSubTab = ref('sudah')
const loading = ref(false)

const kasMasukList = ref([])
const kasKeluarList = ref([])
const userList = ref([])
const selectedUsers = ref([])
const isSelectAll = ref(false)

const searchQuery = ref('')
const totalMasukSummary = ref(0)
const totalKeluarSummary = ref(0)
const globalStats = ref({ pemasukan: 0, pengeluaran: 0, saldo: 0 })
let searchTimeout = null
const role = localStorage.getItem('role')

const filters = reactive({
    bulan: new Date().getMonth() + 1,
    tahun: new Date().getFullYear()
})

const pagination = reactive({
    currentPage: 1,
    limit: 10,
    totalData: 0,
    totalPage: 1
})

const monthNames = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"]

// Forms
const showModalMasuk = ref(false)
const showModalKeluar = ref(false)
const formMasuk = reactive({ id: null, user_id: '', iuran_wajib: 10000, iuran_bebas: 0, status: true })
const formKeluar = reactive({ id: null, keperluan: '', tanggal_pengeluaran: '', total_pengeluaran: 0 })

// --- FETCH FUNCTIONS ---

const fetchGlobalStats = async () => {
    try {
        const token = localStorage.getItem('token')
        const res = await axios.get('http://localhost:6500/api/dashboard/stats', {
            headers: { Authorization: `Bearer ${token}` }
        })
        if (res.data && res.data.cards) {
            globalStats.value.pemasukan = res.data.cards.pemasukan_all_time
            globalStats.value.pengeluaran = res.data.cards.pengeluaran_all_time
            globalStats.value.saldo = res.data.cards.saldo_akhir
        }
    } catch (e) { console.error(e) }
}

const fetchUsers = async () => {
    try {
        const token = localStorage.getItem('token')
        const res = await axios.get('http://localhost:6500/api/auth/users?role=user', {
            headers: { Authorization: `Bearer ${token}` }
        })
        if (res.data.status === 'success') userList.value = res.data.data
    } catch (e) { console.error(e) }
}

const fetchKasMasuk = async () => {
    loading.value = true
    selectedUsers.value = []
    isSelectAll.value = false
    const token = localStorage.getItem('token')
    const endpoint = activeSubTab.value === 'sudah'
        ? 'http://localhost:6500/api/kas/masuk/sudah-bayar'
        : 'http://localhost:6500/api/kas/masuk/belum-bayar'

    try {
        const res = await axios.get(endpoint, {
            headers: { Authorization: `Bearer ${token}` },
            params: {
                bulan: filters.bulan,
                tahun: filters.tahun,
                page: pagination.currentPage,
                limit: pagination.limit,
                search: searchQuery.value
            }
        })
        kasMasukList.value = res.data.data || []

        if (res.data.pagination) {
            pagination.totalData = res.data.pagination.totalData
            pagination.totalPage = res.data.pagination.totalPage
            pagination.currentPage = res.data.pagination.currentPage
        } else {
            // Fallback
            pagination.totalData = kasMasukList.value.length
            pagination.totalPage = 1
            pagination.currentPage = 1
        }

        if (activeSubTab.value === 'sudah' && res.data.summary) {
            totalMasukSummary.value = res.data.summary.total_masuk
        } else {
            totalMasukSummary.value = 0
        }
    } catch (e) {
        kasMasukList.value = []
    } finally {
        loading.value = false
    }
}

const fetchKasKeluar = async () => {
    loading.value = true
    const token = localStorage.getItem('token')
    try {
        const res = await axios.get('http://localhost:6500/api/kas/keluar', {
            headers: { Authorization: `Bearer ${token}` },
            params: {
                bulan: filters.bulan,
                tahun: filters.tahun,
                page: pagination.currentPage,
                limit: pagination.limit,
                search: searchQuery.value
            }
        })
        kasKeluarList.value = res.data.data || []

        if (res.data.pagination) {
            pagination.totalData = res.data.pagination.totalData
            pagination.totalPage = res.data.pagination.totalPage
            pagination.currentPage = res.data.pagination.currentPage
        } else {
            // Fallback
            pagination.totalData = kasKeluarList.value.length
            pagination.totalPage = 1
            pagination.currentPage = 1
        }

        if (res.data.summary) {
            totalKeluarSummary.value = res.data.summary.total_keluar
        } else {
            totalKeluarSummary.value = 0
        }

    } catch (e) {
        kasKeluarList.value = []
    } finally {
        loading.value = false
    }
}

// --- HANDLERS ---

const refreshData = () => {
    pagination.currentPage = 1
    if (activeMainTab.value === 'masuk') fetchKasMasuk()
    else fetchKasKeluar()
}

const changeMainTab = (tab) => {
    activeMainTab.value = tab
    searchQuery.value = ''
    pagination.currentPage = 1
    if (tab === 'masuk') fetchKasMasuk()
    else fetchKasKeluar()
}

const changeSubTab = (tab) => {
    activeSubTab.value = tab
    searchQuery.value = ''
    pagination.currentPage = 1
    fetchKasMasuk()
}

const handleSearch = () => {
    if (searchTimeout) clearTimeout(searchTimeout)
    searchTimeout = setTimeout(() => {
        pagination.currentPage = 1
        if (activeMainTab.value === 'masuk') fetchKasMasuk()
        else fetchKasKeluar()
    }, 500)
}

const changePage = (page) => {
    if (page >= 1 && page <= pagination.totalPage) {
        pagination.currentPage = page
        if (activeMainTab.value === 'masuk') fetchKasMasuk()
        else fetchKasKeluar()
    }
}

// --- CHECKLIST LOGIC ---

const toggleSelectAll = () => {
    if (isSelectAll.value) {
        selectedUsers.value = kasMasukList.value.map(item => item.id)
    } else {
        selectedUsers.value = []
    }
}

// --- MODAL & CRUD LOGIC (MASUK) ---

const openModalBulk = () => {
    if (selectedUsers.value.length === 0) return Swal.fire('Info', 'Pilih minimal satu warga', 'info')
    formMasuk.id = null
    formMasuk.user_id = null
    formMasuk.iuran_wajib = 10000
    formMasuk.iuran_bebas = 0
    showModalMasuk.value = true
}

const openModalMasuk = (mode, item = null) => {
    showModalMasuk.value = true
    if (mode === 'edit' && item) {
        formMasuk.id = item.id
        formMasuk.user_id = item.user_id
        formMasuk.iuran_wajib = item.iuran_wajib
        formMasuk.iuran_bebas = item.iuran_bebas
    } else if (mode === 'pay' && item) {
        formMasuk.id = null
        formMasuk.user_id = item.id
        formMasuk.iuran_wajib = 10000
        formMasuk.iuran_bebas = 0
    } else {
        formMasuk.id = null
        formMasuk.user_id = ''
        formMasuk.iuran_wajib = 10000
        formMasuk.iuran_bebas = 0
    }
}

const submitMasuk = async () => {
    const token = localStorage.getItem('token')
    const periode = `${filters.tahun}-${String(filters.bulan).padStart(2, '0')}-01`
    try {
        if (selectedUsers.value.length > 0 && !formMasuk.id) {
            const payload = {
                user_ids: selectedUsers.value,
                iuran_wajib: formMasuk.iuran_wajib,
                iuran_bebas: formMasuk.iuran_bebas,
                periode: periode,
                status: true
            }
            await axios.post('http://localhost:6500/api/kas/masuk/bulk', payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pembayaran massal berhasil!', 'success')
            selectedUsers.value = []
            isSelectAll.value = false
        } else if (formMasuk.id) {
            const payload = { ...formMasuk, periode, status: true }
            await axios.put(`http://localhost:6500/api/kas/masuk/${formMasuk.id}`, payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Data berhasil diperbarui', 'success')
        } else {
            const payload = { ...formMasuk, periode, status: true }
            await axios.post('http://localhost:6500/api/kas/masuk', payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pembayaran berhasil dicatat', 'success')
        }
        showModalMasuk.value = false
        fetchKasMasuk()
        fetchGlobalStats()
    } catch (e) {
        Swal.fire('Gagal', e.response?.data?.message || 'Terjadi kesalahan', 'error')
    }
}

// --- MODAL & CRUD LOGIC (KELUAR) ---

const openModalKeluar = (mode, item = null) => {
    showModalKeluar.value = true
    if (mode === 'edit' && item) {
        formKeluar.id = item.id
        formKeluar.keperluan = item.keperluan
        formKeluar.tanggal_pengeluaran = item.tanggal_pengeluaran.split('T')[0]
        formKeluar.total_pengeluaran = item.total_pengeluaran
    } else {
        formKeluar.id = null
        formKeluar.keperluan = ''
        formKeluar.tanggal_pengeluaran = new Date().toISOString().split('T')[0]
        formKeluar.total_pengeluaran = 0
    }
}

const submitKeluar = async () => {
    const token = localStorage.getItem('token')
    try {
        if (formKeluar.id) {
            await axios.put(`http://localhost:6500/api/kas/keluar/${formKeluar.id}`, formKeluar, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Data diperbarui', 'success')
        } else {
            await axios.post('http://localhost:6500/api/kas/keluar', formKeluar, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pengeluaran dicatat', 'success')
        }
        showModalKeluar.value = false
        fetchKasKeluar()
        fetchGlobalStats()
    } catch (e) {
        Swal.fire('Gagal', e.response?.data?.message || 'Error', 'error')
    }
}

// --- DELETE ---

const deleteItem = async (type, id) => {
    const result = await Swal.fire({
        title: 'Yakin hapus data ini?', text: 'Data tidak dapat dikembalikan', icon: 'warning', showCancelButton: true, confirmButtonText: 'Ya, Hapus'
    })
    if (result.isConfirmed) {
        const token = localStorage.getItem('token')
        const url = type === 'masuk' ? `http://localhost:6500/api/kas/masuk/${id}` : `http://localhost:6500/api/kas/keluar/${id}`
        try {
            await axios.delete(url, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Terhapus', 'Data berhasil dihapus', 'success')
            if (type === 'masuk') fetchKasMasuk()
            else fetchKasKeluar()
            fetchGlobalStats()
        } catch (e) {
            Swal.fire('Gagal', 'Terjadi kesalahan', 'error')
        }
    }
}

const formatRupiah = (val) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(val)

onMounted(() => {
    fetchUsers()
    fetchKasMasuk()
    fetchGlobalStats()
})
</script>