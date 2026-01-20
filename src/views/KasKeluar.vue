<template>
    <AdminLayout>
        <div class="space-y-6">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Data Pengeluaran</h2>
                    <p class="text-sm text-gray-500 dark:text-gray-400">Kelola arus kas keluar operasional.</p>
                </div>

                <div
                    class="bg-red-50 dark:bg-red-900/20 border border-red-100 dark:border-red-800 p-4 rounded-xl flex items-center gap-4">
                    <div class="p-3 bg-red-100 dark:bg-red-800 rounded-full text-red-600 dark:text-red-200">
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M13 17h8m0 0V9m0 8l-8-8-4 4-6-6"></path>
                        </svg>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 dark:text-gray-400 uppercase font-semibold">Total Keluar ({{
                            monthNames[filters.bulan - 1] }})</p>
                        <h3 class="text-xl font-bold text-red-600 mt-1">{{ formatRupiah(totalKeluarSummary) }}</h3>
                    </div>
                </div>
            </div>

            <div
                class="bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-800 rounded-xl p-5 shadow-sm min-h-[500px]">

                <div class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4 mb-6">
                    <div class="flex flex-col sm:flex-row gap-3 w-full md:w-auto">
                        <div class="relative w-full sm:w-64">
                            <span class="absolute inset-y-0 left-0 flex items-center pl-3">
                                <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor"
                                    viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                        d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                                </svg>
                            </span>
                            <input v-model="searchQuery" @input="handleSearch" type="text"
                                placeholder="Cari Keperluan..."
                                class="w-full pl-9 pr-4 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                        </div>
                        <div class="flex gap-2 w-full md:w-auto">
                            <select v-model="filters.bulan" @change="refreshData"
                                class="block w-full md:w-32 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                <option v-for="(m, i) in monthNames" :key="i" :value="i + 1">{{ m }}</option>
                            </select>
                            <select v-model="filters.tahun" @change="refreshData"
                                class="block w-full md:w-24 px-3 py-2 border rounded-lg text-sm focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white">
                                <option v-for="y in [2023, 2024, 2025, 2026]" :key="y" :value="y">{{ y }}</option>
                            </select>
                        </div>
                    </div>

                    <div class="flex gap-2">
                        <button @click="downloadExcel"
                            class="flex items-center justify-center gap-2 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition whitespace-nowrap">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            Excel
                        </button>
                        <button v-show="role === 'admin'" @click="openModalKeluar('add')"
                            class="flex items-center gap-2 bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm font-medium whitespace-nowrap">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 4v16m8-8H4"></path>
                            </svg>
                            Catat Pengeluaran
                        </button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr
                                class="border-b border-gray-100 dark:border-gray-800 text-gray-500 text-xs uppercase bg-gray-50 dark:bg-gray-800/50">
                                <th class="p-4">Tanggal</th>
                                <th class="p-4">Keperluan</th>
                                <th class="p-4">Nominal</th>
                                <th class="p-4">Penginput</th>
                                <th v-show="role === 'admin'" class="p-4 text-center">Aksi</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-gray-100 dark:divide-gray-800">
                            <tr v-if="loading">
                                <td colspan="5" class="p-6 text-center text-gray-500">Memuat data...</td>
                            </tr>
                            <tr v-else-if="kasKeluarList.length === 0">
                                <td colspan="5" class="p-6 text-center text-gray-500">Belum ada data pengeluaran.</td>
                            </tr>
                            <tr v-else v-for="item in kasKeluarList" :key="item.id"
                                class="hover:bg-gray-50 dark:hover:bg-gray-800/30">
                                <td class="p-4 text-sm text-gray-600 dark:text-gray-400">{{ new
                                    Date(item.tanggal_pengeluaran).toLocaleDateString('id-ID') }}</td>
                                <td class="p-4 text-sm font-medium text-gray-800 dark:text-white">{{ item.keperluan }}
                                </td>
                                <td class="p-4 text-sm font-bold text-red-600">{{ formatRupiah(item.total_pengeluaran)
                                }}</td>
                                <td class="p-4 text-sm font-bold dark:text-white">{{ item.nama_penginput }}</td>
                                <td v-show="role === 'admin'" class="p-4 flex justify-center gap-2">
                                    <button @click="openModalKeluar('edit', item)"
                                        class="text-blue-500 hover:bg-blue-50 p-1.5 rounded"><svg class="w-4 h-4"
                                            fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z">
                                            </path>
                                        </svg></button>
                                    <button @click="deleteItem(item.id)"
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

                <div class="flex justify-between items-center border-t border-gray-200 dark:border-gray-700 pt-4 mt-4">
                    <div class="text-sm text-gray-500">Halaman {{ pagination.currentPage }} dari {{ pagination.totalPage
                    }} (Total {{ pagination.totalData }} Data)</div>
                    <div class="flex gap-2">
                        <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage <= 1"
                            class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Prev</button>
                        <button @click="changePage(pagination.currentPage + 1)"
                            :disabled="pagination.currentPage >= pagination.totalPage"
                            class="px-3 py-1 text-sm border rounded hover:bg-gray-100 disabled:opacity-50 dark:text-white dark:hover:bg-blue-400">Next</button>
                    </div>
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
                            <div class="relative mt-1">
                                <span
                                    class="absolute inset-y-0 left-0 flex items-center pl-3 text-gray-500 dark:text-gray-400">Rp</span>
                                <input type="text" :value="formatInput(formKeluar.total_pengeluaran)"
                                    @input="(e) => handleCurrencyInput(e, formKeluar, 'total_pengeluaran')"
                                    class="w-full pl-10 p-2 border rounded-lg dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                                    required>
                            </div>
                        </div>
                        <div class="flex gap-3 pt-2">
                            <button type="button" @click="showModalKeluar = false"
                                class="flex-1 py-2 border rounded-lg text-gray-600 hover:bg-gray-50 dark:text-white dark:hover:text-black">Batal</button>
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

const loading = ref(false)
const kasKeluarList = ref([])
const searchQuery = ref('')
const totalKeluarSummary = ref(0)
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

const showModalKeluar = ref(false)
const formKeluar = reactive({ id: null, keperluan: '', tanggal_pengeluaran: '', total_pengeluaran: 0, nama_penginput: username })

const fetchKasKeluar = async () => {
    loading.value = true
    const token = localStorage.getItem('token')
    try {
        const res = await axios.get('https://alentest.my.id/laporan/api/kas/keluar', {
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

const refreshData = () => {
    pagination.currentPage = 1
    fetchKasKeluar()
}

const handleSearch = () => {
    if (searchTimeout) clearTimeout(searchTimeout)
    searchTimeout = setTimeout(() => {
        pagination.currentPage = 1
        fetchKasKeluar()
    }, 500)
}

const changePage = (page) => {
    if (page >= 1 && page <= pagination.totalPage) {
        pagination.currentPage = page
        fetchKasKeluar()
    }
}

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
            await axios.put(`https://alentest.my.id/laporan/api/kas/keluar/${formKeluar.id}`, formKeluar, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Data diperbarui', 'success')
        } else {
            await axios.post('https://alentest.my.id/laporan/api/kas/keluar', formKeluar, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Sukses', 'Pengeluaran dicatat', 'success')
        }
        showModalKeluar.value = false
        fetchKasKeluar()
    } catch (e) {
        Swal.fire('Gagal', e.response?.data?.message || 'Error', 'error')
    }
}

const deleteItem = async (id) => {
    const result = await Swal.fire({
        title: 'Yakin hapus data ini?', text: 'Data tidak dapat dikembalikan', icon: 'warning', showCancelButton: true, confirmButtonText: 'Ya, Hapus'
    })
    if (result.isConfirmed) {
        const token = localStorage.getItem('token')
        try {
            await axios.delete(`https://alentest.my.id/laporan/api/kas/keluar/${id}`, { headers: { Authorization: `Bearer ${token}` } })
            Swal.fire('Terhapus', 'Data berhasil dihapus', 'success')
            fetchKasKeluar()
        } catch (e) {
            Swal.fire('Gagal', 'Terjadi kesalahan', 'error')
        }
    }
}

const downloadExcel = async () => {
    try {
        const token = localStorage.getItem('token')
        const response = await axios.get('https://alentest.my.id/laporan/api/kas/download/excel', {
            params: { bulan: filters.bulan, tahun: filters.tahun },
            headers: { Authorization: `Bearer ${token}` },
            responseType: 'blob'
        })
        const url = window.URL.createObjectURL(new Blob([response.data]))
        const link = document.createElement('a')
        link.href = url
        link.setAttribute('download', `Laporan_Pengeluaran_${filters.bulan}-${filters.tahun}.xlsx`)
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
        window.URL.revokeObjectURL(url)
    } catch (error) {
        Swal.fire('Gagal', 'Gagal mengunduh laporan excel', 'error')
    }
}

const formatRupiah = (val) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(val)
const formatInput = (val) => val ? val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".") : ''
const handleCurrencyInput = (event, form, key) => {
    let val = event.target.value.replace(/\D/g, '')
    form[key] = val ? parseInt(val) : 0
    event.target.value = formatInput(form[key])
}

onMounted(() => {
    fetchKasKeluar()
})
</script>