<template>
    <AdminLayout>
        <div class="space-y-2">
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

            <div
                class="bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-800 rounded-xl p-5 shadow-sm min-h-[500px] mt-4">
                <div class="space-y-6">
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
                            <button @click="downloadExcel" v-show="role == 'admin'"
                                class="flex items-center justify-center gap-2 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition whitespace-nowrap">
                                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                        d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                    </path>
                                </svg>
                                Excel
                            </button>
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
                                    <th class="p-4" v-if="activeSubTab === 'sudah'">Penginput</th>
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
                                        <td class="p-4 text-sm font-medium dark:text-green-400 text-green-700">{{
                                            formatRupiah(item.iuran_wajib) }}</td>
                                        <td class="p-4 text-sm font-medium  dark:text-white">{{
                                            formatRupiah(item.iuran_bebas) }}</td>
                                        <td class="p-4 text-sm font-medium dark:text-white">{{ item.nama_penginput }}
                                        </td>
                                    </template>
                                    <td class="p-4 text-right">
                                        <span v-if="activeSubTab === 'sudah'"
                                            class="px-2 py-1 rounded-full text-xs font-bold bg-green-100 text-green-700">LUNAS</span>
                                        <span v-else class=" text-xs text-red-500">Belum bayar</span>
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
                                            <button @click="deleteItem(item.id)"
                                                class="text-red-500 hover:bg-red-50 p-1.5 rounded"><svg class="w-4 h-4"
                                                    fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round"
                                                        stroke-width="2"
                                                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                                                    </path>
                                                </svg></button>
                                        </template>
                                        <template v-else>
                                            <div class="flex gap-2">
                                                <button @click="openModalMasuk('pay', item)"
                                                    class="px-3 py-1 bg-blue-600 text-white text-xs rounded hover:bg-blue-700">Bayar</button>
                                                <button @click="sendWhatsAppReminder(item)"
                                                    class="px-3 py-1 bg-green-500 text-white text-xs rounded hover:bg-green-600 flex items-center gap-1">
                                                    <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                                                        <path
                                                            d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.245 2.248 3.481 5.236 3.48 8.414-.003 6.557-5.338 11.892-11.893 11.892-1.99-.001-3.951-.5-5.688-1.448l-6.305 1.654zm6.597-3.807c1.676.995 3.276 1.591 5.392 1.592 5.448 0 9.886-4.434 9.889-9.885.002-5.462-4.415-9.89-9.881-9.892-5.452 0-9.887 4.434-9.889 9.884-.001 2.225.651 3.891 1.746 5.634l-.999 3.648 3.742-.981zm11.387-5.464c-.074-.124-.272-.198-.57-.347-.297-.149-1.758-.868-2.031-.967-.272-.099-.47-.149-.669.149-.198.297-.768.967-.941 1.165-.173.198-.347.223-.644.074-.297-.149-1.255-.462-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.297-.347.446-.521.151-.172.2-.296.3-.495.099-.198.05-.372-.025-.521-.075-.148-.669-1.611-.916-2.206-.242-.579-.487-.501-.669-.51l-.57-.01c-.198 0-.52.074-.792.372s-1.04 1.016-1.04 2.479 1.065 2.876 1.213 3.074c.149.198 2.095 3.2 5.076 4.487.709.306 1.263.489 1.694.626.712.226 1.36.194 1.872.118.571-.085 1.758-.719 2.006-1.413.248-.695.248-1.29.173-1.414z" />
                                                    </svg>
                                                    WA
                                                </button>
                                            </div>
                                        </template>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>

                    <div
                        class="flex justify-between items-center border-t border-gray-200 dark:border-gray-700 pt-4 mt-4">
                        <div class="text-sm text-gray-500">Halaman {{ pagination.currentPage }} dari {{
                            pagination.totalPage }} (Total {{
                                pagination.totalData }} Data)</div>
                        <div class="flex gap-2">
                            <button @click="changePage(pagination.currentPage - 1)"
                                :disabled="pagination.currentPage <= 1"
                                class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Prev</button>
                            <button @click="changePage(pagination.currentPage + 1)"
                                :disabled="pagination.currentPage >= pagination.totalPage"
                                class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Next</button>
                        </div>
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
                                <div class="relative mt-1">
                                    <span
                                        class="absolute inset-y-0 left-0 flex items-center pl-3 text-gray-500 dark:text-gray-400">Rp</span>
                                    <input type="text" :value="formatInput(formMasuk.iuran_wajib)"
                                        @input="(e) => handleCurrencyInput(e, formMasuk, 'iuran_wajib')"
                                        class="w-full pl-10 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white">
                                </div>
                            </div>
                            <div>
                                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Sukarela</label>
                                <div class="relative mt-1">
                                    <span
                                        class="absolute inset-y-0 left-0 flex items-center pl-3 text-gray-500 dark:text-gray-400">Rp</span>
                                    <input type="text" :value="formatInput(formMasuk.iuran_bebas)"
                                        @input="(e) => handleCurrencyInput(e, formMasuk, 'iuran_bebas')"
                                        class="w-full pl-10 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white">
                                </div>
                            </div>
                        </div>
                        <div class="text-sm text-gray-500">Periode: {{ monthNames[filters.bulan - 1] }} {{ filters.tahun
                        }}</div>
                        <div class="flex gap-3 pt-2">
                            <button type="button" @click="showModalMasuk = false"
                                class="flex-1 py-2 border rounded-lg text-gray-600 hover:bg-gray-50 dark:text-white dark:hover:text-black">Batal</button>
                            <button type="submit"
                                class="flex-1 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 font-medium">Simpan</button>
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

const activeSubTab = ref('sudah')
const loading = ref(false)

const kasMasukList = ref([])
const userList = ref([])
const selectedUsers = ref([])
const isSelectAll = ref(false)

const searchQuery = ref('')
const totalMasukSummary = ref(0)
const globalStats = ref({ pemasukan: 0, pengeluaran: 0, saldo: 0 })
let searchTimeout = null
const role = localStorage.getItem('role')
const username = localStorage.getItem('username')
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

const showModalMasuk = ref(false)
const formMasuk = reactive({ id: null, user_id: '', iuran_wajib: 10000, iuran_bebas: 0, status: true, nama_penginput: username })

const fetchGlobalStats = async () => {
    try {
        const token = localStorage.getItem('token')
        const res = await axios.get('https://alentest.my.id/laporan/api/dashboard/stats', {
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
        const res = await axios.get('https://alentest.my.id/laporan/api/auth/users?role=user', {
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
        ? 'https://alentest.my.id/laporan/api/kas/masuk/sudah-bayar'
        : 'https://alentest.my.id/laporan/api/kas/masuk/belum-bayar'

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

const refreshData = () => {
    pagination.currentPage = 1
    fetchKasMasuk()
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
        fetchKasMasuk()
    }, 500)
}

const changePage = (page) => {
    if (page >= 1 && page <= pagination.totalPage) {
        pagination.currentPage = page
        fetchKasMasuk()
    }
}

const toggleSelectAll = () => {
    if (isSelectAll.value) {
        selectedUsers.value = kasMasukList.value.map(item => item.id)
    } else {
        selectedUsers.value = []
    }
}

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
                nama_penginput: localStorage.getItem('username'),
                status: true
            }
            await axios.post('https://alentest.my.id/laporan/api/kas/masuk/bulk', payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pembayaran massal berhasil!', 'success')
            selectedUsers.value = []
            isSelectAll.value = false
        } else if (formMasuk.id) {
            const payload = { ...formMasuk, periode, status: true }
            await axios.put(`https://alentest.my.id/laporan/api/kas/masuk/${formMasuk.id}`, payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Data berhasil diperbarui', 'success')
        } else {
            const payload = { ...formMasuk, periode, status: true }
            await axios.post('https://alentest.my.id/laporan/api/kas/masuk', payload, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pembayaran berhasil dicatat', 'success')
        }
        showModalMasuk.value = false
        fetchKasMasuk()
        fetchGlobalStats()
    } catch (e) {
        Swal.fire('Gagal', e.response?.data?.message || 'Terjadi kesalahan', 'error')
    }
}

const deleteItem = async (id) => {
    const result = await Swal.fire({
        title: 'Yakin hapus data ini?', text: 'Data tidak dapat dikembalikan', icon: 'warning', showCancelButton: true, confirmButtonText: 'Ya, Hapus'
    })
    if (result.isConfirmed) {
        const token = localStorage.getItem('token')
        try {
            await axios.delete(`https://alentest.my.id/laporan/api/kas/masuk/${id}`, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Terhapus', 'Data berhasil dihapus', 'success')
            fetchKasMasuk()
            fetchGlobalStats()
        } catch (e) {
            Swal.fire('Gagal', 'Terjadi kesalahan', 'error')
        }
    }
}

const downloadExcel = async () => {
    try {
        const token = localStorage.getItem('token')
        const response = await axios.get('https://alentest.my.id/laporan/api/kas/download/excel', {
            params: {
                bulan: filters.bulan,
                tahun: filters.tahun
            },
            headers: { Authorization: `Bearer ${token}` },
            responseType: 'blob'
        })
        const url = window.URL.createObjectURL(new Blob([response.data]))
        const link = document.createElement('a')
        link.href = url
        link.setAttribute('download', `Laporan_Keuangan_${filters.bulan}-${filters.tahun}.xlsx`)
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
        window.URL.revokeObjectURL(url)
    } catch (error) {
        Swal.fire('Gagal', 'Gagal mengunduh laporan excel', 'error')
    }
}

const sendWhatsAppReminder = (item) => {
    if (!item.phone) {
        Swal.fire('Gagal', 'Nomor telepon tidak tersedia', 'error')
        return
    }
    const monthIndex = filters.bulan - 1
    const monthName = monthNames[monthIndex]
    const message = `Halo Bapak/Ibu ${item.username}, mengingatkan untuk pembayaran iuran kas RT bulan ${monthName} ${filters.tahun}. Terima kasih.`

    let phone = item.phone.toString().replace(/\D/g, '')
    if (phone.startsWith('0')) {
        phone = '62' + phone.slice(1)
    }
    window.open(`https://wa.me/${phone}?text=${encodeURIComponent(message)}`, '_blank')
}

const formatRupiah = (val) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(val)
const formatInput = (val) => val ? val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".") : ''
const handleCurrencyInput = (event, form, key) => {
    let val = event.target.value.replace(/\D/g, '')
    form[key] = val ? parseInt(val) : 0
    event.target.value = formatInput(form[key])
}

onMounted(() => {
    fetchUsers()
    fetchKasMasuk()
    fetchGlobalStats()
})
</script>