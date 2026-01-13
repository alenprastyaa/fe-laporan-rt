<template>
  <AdminLayout>
    <div class="mx-auto max-w-screen-2xl bg-gray-50 dark:bg-gray-900 min-h-screen">
      <div class="mb-6 flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between"></div>

      <div v-if="error" class="mb-6 p-4 bg-red-100 text-red-700 rounded-lg border border-red-200">
        {{ error }}
      </div>

      <div class="grid grid-cols-12 gap-4 md:gap-6 2xl:gap-7.5">

        <div
          class="col-span-12 xl:col-span-8 rounded-2xl border border-gray-200 bg-white px-5 pb-5 pt-5 dark:border-gray-800 dark:bg-gray-900 sm:px-6 sm:pt-6">
          <div class="flex flex-col gap-5 mb-6 sm:flex-row sm:justify-between">
            <div class="w-full">
              <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Statistik Arus Kas</h3>
              <p class="mt-1 text-gray-500 text-theme-sm dark:text-gray-400">Grafik Pemasukan & Pengeluaran Tahun Ini
              </p>
            </div>
          </div>
          <div class="max-w-full overflow-x-auto custom-scrollbar">
            <div id="chartOne" class="-ml-4 min-w-[650px] xl:min-w-full pl-2">
              <VueApexCharts type="area" height="310" :options="areaChartOptions" :series="areaSeries" />
            </div>
          </div>
        </div>

        <div
          class="col-span-12 xl:col-span-4 rounded-2xl border border-gray-200 bg-white px-5 pt-5 pb-11 dark:border-gray-800 dark:bg-gray-900 sm:px-6 sm:pt-6">
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Kepatuhan Warga (Bulan Ini)</h3>
            <p class="mt-1 text-gray-500 text-theme-sm dark:text-gray-400">
              Total Warga: {{ stats.total_warga }} Orang
            </p>
          </div>

          <div class="relative max-h-[195px] mb-8">
            <div class="h-full flex justify-center">
              <VueApexCharts type="radialBar" height="330" :options="radialChartOptions" :series="complianceSeries" />
            </div>
          </div>

          <p class="mx-auto mt-10 w-full max-w-[380px] text-center text-sm text-gray-500 sm:text-base mb-6">
            <span class="font-bold text-gray-800 dark:text-white">{{ wargaSudahBayar }}</span> warga telah membayar.
            Tersisa <span class="font-bold text-red-500">{{ stats.warga_menunggak }}</span> warga yang belum lunas.
          </p>

          <div class="grid grid-cols-2 gap-4 border-t border-gray-100 dark:border-gray-800 pt-4">
            <div class="text-center p-2 rounded-lg bg-blue-50 dark:bg-blue-900/20">
              <p class="mb-1 text-blue-600 dark:text-blue-400 text-xs font-medium">Total Kas Wajib (Tahun Ini)</p>
              <p class="text-sm font-bold text-gray-800 dark:text-white/90">
                {{ formatCurrency(stats.pemasukan_wajib_tahun_ini) }}
              </p>
            </div>
            <div class="text-center p-2 rounded-lg bg-green-50 dark:bg-green-900/20">
              <p class="mb-1 text-green-600 dark:text-green-400 text-xs font-medium">Total Sukarela (Tahun Ini)</p>
              <p class="text-sm font-bold text-gray-800 dark:text-white/90">
                {{ formatCurrency(stats.pemasukan_sukarela_tahun_ini) }}
              </p>
            </div>
          </div>
        </div>

        <div class="col-span-12 xl:col-span-5 space-y-6">
          <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-gray-900 sm:p-6">
            <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90 mb-4">Status Pembayaran</h3>
            <div class="mt-4 space-y-3">
              <div class="w-full bg-gray-200 rounded-full h-2.5 dark:bg-gray-700 mb-4">
                <div class="bg-blue-600 h-2.5 rounded-full" :style="{ width: complianceSeries[0] + '%' }"></div>
              </div>

              <div class="flex items-center justify-between p-3 bg-green-50 dark:bg-green-900/20 rounded-lg">
                <div class="flex items-center gap-3">
                  <div class="w-2 h-2 rounded-full bg-green-500"></div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Sudah Bayar</span>
                </div>
                <span class="text-sm font-bold text-green-600">{{ wargaSudahBayar }} Orang</span>
              </div>
              <div class="flex items-center justify-between p-3 bg-red-50 dark:bg-red-900/20 rounded-lg">
                <div class="flex items-center gap-3">
                  <div class="w-2 h-2 rounded-full bg-red-500"></div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Menunggak</span>
                </div>
                <span class="text-sm font-bold text-red-500">{{ stats.warga_menunggak }} Orang</span>
              </div>
            </div>
          </div>

          <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-gray-900 sm:p-6">
            <div class="flex items-center justify-between mb-4">
              <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Daftar Menunggak (Bulan Ini)</h3>
              <router-link to="/laporan-kas" class="text-xs text-blue-600 hover:underline">Lihat Semua</router-link>
            </div>
            <div class="space-y-4 max-h-[300px] overflow-y-auto custom-scrollbar pr-1">
              <div v-if="wargaBelumBayar.length === 0" class="text-center py-4 text-sm text-gray-500">
                Semua warga sudah bayar ✨
              </div>
              <div v-for="(warga, index) in wargaBelumBayar" :key="index"
                class="flex items-center justify-between p-3 border border-gray-50 dark:border-gray-800 rounded-xl hover:bg-gray-50 dark:hover:bg-gray-800/50 transition">
                <div class="flex items-center gap-3">
                  <div
                    class="h-9 w-9 rounded-full bg-red-100 dark:bg-red-900/20 text-red-600 flex items-center justify-center font-bold text-xs shrink-0">
                    {{ warga.username.charAt(0).toUpperCase() }}
                  </div>
                  <div class="min-w-0">
                    <p class="text-sm font-semibold text-gray-800 dark:text-white truncate">{{ warga.username }}</p>
                    <p class="text-xs text-gray-500 truncate">{{ warga.phone || 'No Phone' }}</p>
                  </div>
                </div>
                <button v-show="role == 'admin'" @click="sendWhatsAppReminder(warga)"
                  class="p-2 bg-green-500 hover:bg-green-600 text-white rounded-lg transition-colors shrink-0"
                  title="Kirim Pengingat WA">
                  <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                    <path
                      d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.246 2.248 3.484 5.232 3.484 8.412-.003 6.557-5.338 11.892-11.893 11.892-1.997-.001-3.951-.5-5.688-1.448l-6.309 1.656zm6.29-4.464c1.56.925 3.278 1.411 5.037 1.413 5.564 0 10.091-4.527 10.093-10.093 0-2.696-1.05-5.231-2.956-7.138-1.907-1.907-4.442-2.956-7.137-2.956-5.566 0-10.093 4.527-10.093 10.094 0 1.896.536 3.717 1.548 5.301l-.95 3.468 3.558-.934zm11.333-7.391c-.302-.151-1.785-.881-2.062-.982-.277-.1-.478-.151-.68.151-.202.302-.782.982-.957 1.183-.176.202-.352.226-.654.076-.302-.151-1.274-.47-2.426-1.5-.897-.8-1.502-1.79-1.678-2.091-.176-.302-.019-.465.131-.614.136-.134.302-.352.453-.528.151-.176.202-.302.302-.503.1-.202.05-.377-.025-.528-.076-.151-.68-1.641-.932-2.244-.247-.587-.499-.508-.68-.517-.176-.008-.377-.01-.579-.01-.202 0-.528.076-.805.377-.277.302-1.057 1.031-1.057 2.515 0 1.485 1.082 2.92 1.232 3.121.151.202 2.13 3.253 5.159 4.561.72.311 1.282.496 1.719.635.722.23 1.38.197 1.9.12.58-.086 1.785-.73 2.037-1.435.252-.704.252-1.308.176-1.435-.076-.127-.277-.202-.579-.353z" />
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>

        <div
          class="col-span-12 xl:col-span-7 rounded-2xl border border-gray-200 bg-white px-4 pb-3 pt-4 dark:border-gray-800 dark:bg-gray-900 sm:px-6">
          <div class="flex items-center justify-between mb-4">
            <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Aktivitas Terbaru</h3>
          </div>
          <div class="max-w-full overflow-x-auto">
            <table class="min-w-full">
              <thead>
                <tr class="border-b border-gray-100 dark:border-gray-800">
                  <th class="py-3 text-left font-medium text-gray-500 text-sm">Keterangan</th>
                  <th class="py-3 text-left font-medium text-gray-500 text-sm">Tipe</th>
                  <th class="py-3 text-left font-medium text-gray-500 text-sm">Nominal</th>
                  <th class="py-3 text-left font-medium text-gray-500 text-sm">Tanggal</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in recentActivity" :key="index"
                  class="border-b border-gray-50 dark:border-gray-800 last:border-none hover:bg-gray-50 dark:hover:bg-gray-800/50 transition-colors">

                  <td class="py-4 px-2 md:py-3">
                    <div class="flex items-center gap-3">
                      <div class="h-10 w-10 rounded-full flex items-center justify-center flex-shrink-0"
                        :class="item.type === 'masuk' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600'">
                        <span v-if="item.type === 'masuk'">↓</span>
                        <span v-else>↑</span>
                      </div>
                      <span
                        class="text-sm font-medium text-gray-800 dark:text-white truncate max-w-[150px] sm:max-w-xs">{{
                          item.description }}</span>
                    </div>
                  </td>

                  <td class="py-4 px-2 md:py-3">
                    <span class="text-xs font-medium px-3 py-1.5 rounded-full inline-block"
                      :class="item.type === 'masuk' ? 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300' : 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300'">
                      {{ item.type === 'masuk' ? 'Pemasukan' : 'Pengeluaran' }}
                    </span>
                  </td>
                  <td class="py-4 px-2 md:py-3 text-sm text-gray-600 dark:text-gray-300">
                    {{ formatCurrency(item.amount) }}
                  </td>
                  <td class="py-4 px-2 md:py-3 text-sm text-gray-500 dark:text-gray-400">
                    {{ new Date(item.date).toLocaleDateString('id-ID') }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <a href="https://wa.me/6281294233137" target="_blank"
      class="fixed bottom-6 right-6 z-[9999] group flex items-center">
      <span
        class="mr-3 px-4 py-2 bg-white dark:bg-gray-800 text-gray-700 dark:text-gray-200 text-sm font-semibold rounded-xl shadow-lg border border-gray-100 dark:border-gray-700 opacity-0 group-hover:opacity-100 transition-all duration-300 transform translate-x-2 group-hover:translate-x-0 pointer-events-none">
        Chat Admin RT
      </span>

      <div
        class="flex items-center justify-center w-10 h-10 bg-[#25D366] hover:bg-[#20ba5a] text-white rounded-2xl shadow-[0_8px_30px_rgb(37,211,102,0.4)] transition-all duration-300 hover:scale-105 active:scale-95">
        <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
          <path
            d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.246 2.248 3.484 5.232 3.484 8.412-.003 6.557-5.338 11.892-11.893 11.892-1.997-.001-3.951-.5-5.688-1.448l-6.309 1.656zm6.29-4.464c1.56.925 3.278 1.411 5.037 1.413 5.564 0 10.091-4.527 10.093-10.093 0-2.696-1.05-5.231-2.956-7.138-1.907-1.907-4.442-2.956-7.137-2.956-5.566 0-10.093 4.527-10.093 10.094 0 1.896.536 3.717 1.548 5.301l-.95 3.468 3.558-.934zm11.333-7.391c-.302-.151-1.785-.881-2.062-.982-.277-.1-.478-.151-.68.151-.202.302-.782.982-.957 1.183-.176.202-.352.226-.654.076-.302-.151-1.274-.47-2.426-1.5-.897-.8-1.502-1.79-1.678-2.091-.176-.302-.019-.465.131-.614.136-.134.302-.352.453-.528.151-.176.202-.302.302-.503.1-.202.05-.377-.025-.528-.076-.151-.68-1.641-.932-2.244-.247-.587-.499-.508-.68-.517-.176-.008-.377-.01-.579-.01-.202 0-.528.076-.805.377-.277.302-1.057 1.031-1.057 2.515 0 1.485 1.082 2.92 1.232 3.121.151.202 2.13 3.253 5.159 4.561.72.311 1.282.496 1.719.635.722.23 1.38.197 1.9.12.58-.086 1.785-.73 2.037-1.435.252-.704.252-1.308.176-1.435-.076-.127-.277-.202-.579-.353z" />
        </svg>
      </div>
    </a>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'
import VueApexCharts from 'vue3-apexcharts'
import { useRouter } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue';

const role = localStorage.getItem('role')
const router = useRouter()

// Interface Updated with separate Fields
interface RecentActivityItem {
  type: string;
  description: string;
  amount: number;
  date: string;
}

interface StatsData {
  total_warga: number;
  saldo_akhir: number;
  pemasukan_all_time: number;
  pengeluaran_all_time: number;
  warga_menunggak: number;
  // Field Baru dari Backend
  pemasukan_wajib_tahun_ini: number;
  pemasukan_sukarela_tahun_ini: number;
}

interface WargaTunggakan {
  username: string;
  phone: string;
}

const isLoading = ref(false)
const error = ref('')
const stats = ref<StatsData>({
  total_warga: 0,
  saldo_akhir: 0,
  pemasukan_all_time: 0,
  pengeluaran_all_time: 0,
  warga_menunggak: 0,
  pemasukan_wajib_tahun_ini: 0,
  pemasukan_sukarela_tahun_ini: 0
})

const recentActivity = ref<RecentActivityItem[]>([])
const wargaBelumBayar = ref<WargaTunggakan[]>([])
const monthlyIncome = ref(0) // Masih dipakai untuk data chart series jika perlu

// Area Series (Line Chart Pemasukan vs Pengeluaran)
const areaSeries = ref([
  { name: 'Pemasukan', data: [] as number[] },
  { name: 'Pengeluaran', data: [] as number[] }
])

// Computed: Jumlah Warga yang Sudah Bayar
const wargaSudahBayar = computed(() => {
  return Math.max(0, stats.value.total_warga - stats.value.warga_menunggak)
})

// Computed Series untuk Radial Chart (Persentase Orang yg Bayar)
const complianceSeries = computed(() => {
  const total = stats.value.total_warga
  if (total === 0) return [0]

  const paidCount = total - stats.value.warga_menunggak
  const percent = (paidCount / total) * 100

  return [Math.min(parseFloat(percent.toFixed(1)), 100)]
})

// Options Chart
const areaChartOptions = ref({
  legend: { show: true, position: 'top', horizontalAlign: 'left' },
  colors: ['#3C50E0', '#80CAEE'],
  chart: { fontFamily: 'Satoshi, sans-serif', height: 335, type: 'area', toolbar: { show: false } },
  fill: { gradient: { opacityFrom: 0.55, opacityTo: 0 } },
  stroke: { curve: 'smooth', width: 2 },
  grid: { strokeDashArray: 5, xaxis: { lines: { show: false } }, yaxis: { lines: { show: true } } },
  dataLabels: { enabled: false },
  xaxis: {
    type: 'category',
    categories: ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agu', 'Sep', 'Okt', 'Nov', 'Des'],
    axisBorder: { show: false },
    axisTicks: { show: false }
  }
})

const radialChartOptions = ref({
  colors: ['#10B981'], // Warna Hijau untuk "Sudah Bayar"
  chart: { fontFamily: 'Satoshi, sans-serif', type: 'radialBar', height: 335 },
  plotOptions: {
    radialBar: {
      startAngle: -90,
      endAngle: 90,
      hollow: { size: '60%' },
      track: { background: '#E2E8F0', strokeWidth: '97%' },
      dataLabels: {
        name: { show: true, label: 'Lunas', color: '#64748B', offsetY: 20 },
        value: { offsetY: -20, fontSize: '30px', fontWeight: '600', color: '#1C2434' }
      }
    }
  },
  fill: { type: 'solid' },
  stroke: { lineCap: 'round' },
  labels: ['Lunas']
})

// Helper Formatter
const formatCurrency = (value: number) => {
  return new Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR',
    minimumFractionDigits: 0
  }).format(value)
}

const sendWhatsAppReminder = (warga: WargaTunggakan) => {
  const month = new Intl.DateTimeFormat('id-ID', { month: 'long' }).format(new Date());
  const message = `Halo Bapak/Ibu ${warga.username}, mengingatkan untuk pembayaran iuran kas RT bulan ${month}. Terima kasih.`;
  const phone = warga.phone.startsWith('0') ? '62' + warga.phone.slice(1) : warga.phone;
  window.open(`https://wa.me/${phone}?text=${encodeURIComponent(message)}`, '_blank');
}

// Fetch Data
const fetchData = async () => {
  isLoading.value = true
  error.value = ''
  try {
    const token = localStorage.getItem('token')
    if (!token) throw new Error("No token found")

    const config = { headers: { Authorization: `Bearer ${token}` } }
    const currentMonthIndex = new Date().getMonth()
    const params = {
      bulan: currentMonthIndex + 1,
      tahun: new Date().getFullYear(),
      limit: 5
    }

    const [statsRes, recentRes, belumBayarRes] = await Promise.all([
      axios.get('https://alentest.my.id/laporan/api/dashboard/stats', config),
      axios.get('https://alentest.my.id/laporan/api/dashboard/recent', config),
      axios.get('https://alentest.my.id/laporan/api/kas/masuk/belum-bayar', { ...config, params })
    ])

    const dStats = statsRes.data
    // Update State dengan data baru termasuk Wajib & Sukarela
    stats.value = {
      total_warga: dStats.cards.total_warga,
      saldo_akhir: dStats.cards.saldo_akhir,
      pemasukan_all_time: dStats.cards.pemasukan_all_time,
      pengeluaran_all_time: dStats.cards.pengeluaran_all_time,
      warga_menunggak: dStats.cards.warga_menunggak_bulan_ini,
      pemasukan_wajib_tahun_ini: dStats.cards.pemasukan_wajib_tahun_ini,     // MAPPING BARU
      pemasukan_sukarela_tahun_ini: dStats.cards.pemasukan_sukarela_tahun_ini // MAPPING BARU
    }

    areaSeries.value = [
      { name: 'Pemasukan', data: dStats.chart.pemasukan },
      { name: 'Pengeluaran', data: dStats.chart.pengeluaran }
    ]

    monthlyIncome.value = dStats.chart.pemasukan[currentMonthIndex] || 0
    recentActivity.value = recentRes.data
    wargaBelumBayar.value = belumBayarRes.data.data

  } catch (err: any) {
    console.error("Error fetching data:", err)
    error.value = "Gagal mengambil data dashboard."
    if (axios.isAxiosError(err) && err.response?.status === 401) {
      localStorage.removeItem('token')
    }
    router.push('/signin')
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchData()
})
</script>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  height: 6px;
  width: 6px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}
</style>