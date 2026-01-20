<template>
  <AdminLayout>
    <div>
      <div class="p-5 mb-6 border border-gray-200 rounded-2xl dark:border-gray-800 lg:p-6">
        <div class="flex flex-col gap-6 lg:flex-row lg:items-start lg:justify-between">
          <div>
            <h4 class="text-lg font-semibold text-gray-800 dark:text-white/90 lg:mb-6">
              Personal Information
            </h4>

            <div class="grid grid-cols-1 gap-4 lg:grid-cols-2 lg:gap-7 2xl:gap-x-32">
              <div>
                <p class="mb-2 text-xs leading-normal text-gray-500 dark:text-gray-400">Username / First Name</p>
                <p class="text-sm font-medium text-gray-800 dark:text-white/90">{{ user.firstName }}</p>
              </div>

              <div>
                <p class="mb-2 text-xs leading-normal text-gray-500 dark:text-gray-400">Role</p>
                <p class="text-sm font-medium text-gray-800 dark:text-white/90 uppercase">{{ user.role }}</p>
              </div>

              <div>
                <p class="mb-2 text-xs leading-normal text-gray-500 dark:text-gray-400">Phone</p>
                <p class="text-sm font-medium text-gray-800 dark:text-white/90">{{ user.phone }}</p>
              </div>

              <div>
                <p class="mb-2 text-xs leading-normal text-gray-500 dark:text-gray-400">RT Info</p>
                <p class="text-sm font-medium text-gray-800 dark:text-white/90">{{ user.bio }}</p>
              </div>
            </div>
          </div>

          <div class="flex gap-3">
            <button
              class="flex items-center gap-2 px-4 py-2 text-sm font-medium text-white bg-blue-600 rounded-lg hover:bg-blue-700 transition-colors"
              @click="isChangePasswordModal = true">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z">
                </path>
              </svg>
              Ubah Password
            </button>
            <!-- <button
              class="flex items-center gap-2 px-4 py-2 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors dark:bg-gray-800 dark:text-gray-400 dark:border-gray-700 dark:hover:bg-white/5"
              @click="openEditModal">
              <svg class="fill-current" width="18" height="18" viewBox="0 0 18 18" fill="none"
                xmlns="http://www.w3.org/2000/svg">
                <path fill-rule="evenodd" clip-rule="evenodd"
                  d="M15.0911 2.78206C14.2125 1.90338 12.7878 1.90338 11.9092 2.78206L4.57524 10.116C4.26682 10.4244 4.0547 10.8158 3.96468 11.2426L3.31231 14.3352C3.25997 14.5833 3.33653 14.841 3.51583 15.0203C3.69512 15.1996 3.95286 15.2761 4.20096 15.2238L7.29355 14.5714C7.72031 14.4814 8.11172 14.2693 8.42013 13.9609L15.7541 6.62695C16.6327 5.74827 16.6327 4.32365 15.7541 3.44497L15.0911 2.78206ZM12.9698 3.84272C13.2627 3.54982 13.7376 3.54982 14.0305 3.84272L14.6934 4.50563C14.9863 4.79852 14.9863 5.2734 14.6934 5.56629L14.044 6.21573L12.3204 4.49215L12.9698 3.84272ZM11.2597 5.55281L5.6359 11.1766C5.53309 11.2794 5.46238 11.4099 5.43238 11.5522L5.01758 13.5185L6.98394 13.1037C7.1262 13.0737 7.25666 13.003 7.35947 12.9002L12.9833 7.27639L11.2597 5.55281Z"
                  fill="" />
              </svg>
              Edit
            </button> -->
          </div>
        </div>
      </div>

      <Teleport to="body">
        <Transition name="fade">
          <div v-if="isProfileInfoModal" class="fixed inset-0 z-40 bg-black/50 flex items-center justify-center p-4">
            <Transition name="scale">
              <div class="relative w-full max-w-[700px] rounded-3xl bg-white dark:bg-gray-900 shadow-2xl"
                v-if="isProfileInfoModal">
                <button @click="isProfileInfoModal = false"
                  class="transition-all absolute right-5 top-5 z-50 flex h-11 w-11 items-center justify-center rounded-full bg-gray-100 text-gray-400 hover:bg-gray-200 hover:text-gray-600 dark:bg-white/10 dark:text-gray-400 dark:hover:bg-white/20 dark:hover:text-gray-300">
                  <svg class="fill-current" width="24" height="24" viewBox="0 0 24 24" fill="none"
                    xmlns="http://www.w3.org/2000/svg">
                    <path fill-rule="evenodd" clip-rule="evenodd"
                      d="M6.04289 16.5418C5.65237 16.9323 5.65237 17.5655 6.04289 17.956C6.43342 18.3465 7.06658 18.3465 7.45711 17.956L11.9987 13.4144L16.5408 17.9565C16.9313 18.347 17.5645 18.347 17.955 17.9565C18.3455 17.566 18.3455 16.9328 17.955 16.5423L13.4129 12.0002L17.955 7.45808C18.3455 7.06756 18.3455 6.43439 17.955 6.04387C17.5645 5.65335 16.9313 5.65335 16.5408 6.04387L11.9987 10.586L7.45711 6.04439C7.06658 5.65386 6.43342 5.65386 6.04289 6.04439C5.65237 6.43491 5.65237 7.06808 6.04289 7.4586L10.5845 12.0002L6.04289 16.5418Z"
                      fill="currentColor" />
                  </svg>
                </button>

                <div class="p-4 lg:p-8">
                  <div class="px-2 pr-14 mb-6">
                    <h4 class="mb-2 text-2xl font-semibold text-gray-800 dark:text-white/90">
                      Edit Personal Information
                    </h4>
                    <p class="text-sm text-gray-500 dark:text-gray-400">
                      Update your details to keep your profile up-to-date.
                    </p>
                  </div>

                  <form @submit.prevent="saveProfile" class="flex flex-col">
                    <div class="max-h-[500px] overflow-y-auto px-2 mb-6 space-y-7">
                      <div>
                        <h5 class="mb-4 text-lg font-semibold text-gray-800 dark:text-white/90">
                          Personal Information
                        </h5>
                        <div class="grid grid-cols-1 gap-4 lg:grid-cols-2">
                          <div>
                            <label class="mb-2 block text-sm font-medium text-gray-700 dark:text-gray-300">First
                              Name</label>
                            <input type="text" v-model="form.firstName" placeholder="Your first name"
                              class="w-full h-11 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20 dark:border-gray-700 dark:bg-gray-800 dark:text-white/90 dark:placeholder:text-white/50 dark:focus:border-blue-400" />
                          </div>
                          <div>
                            <label class="mb-2 block text-sm font-medium text-gray-700 dark:text-gray-300">Last
                              Name</label>
                            <input type="text" v-model="form.lastName" placeholder="Your last name"
                              class="w-full h-11 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20 dark:border-gray-700 dark:bg-gray-800 dark:text-white/90 dark:placeholder:text-white/50 dark:focus:border-blue-400" />
                          </div>
                          <div>
                            <label class="mb-2 block text-sm font-medium text-gray-700 dark:text-gray-300">Email
                              Address</label>
                            <input type="email" v-model="form.email" placeholder="your.email@example.com"
                              class="w-full h-11 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20 dark:border-gray-700 dark:bg-gray-800 dark:text-white/90 dark:placeholder:text-white/50 dark:focus:border-blue-400" />
                          </div>
                          <div>
                            <label class="mb-2 block text-sm font-medium text-gray-700 dark:text-gray-300">Phone</label>
                            <input type="text" v-model="form.phone" placeholder="+1 (555) 000-0000"
                              class="w-full h-11 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20 dark:border-gray-700 dark:bg-gray-800 dark:text-white/90 dark:placeholder:text-white/50 dark:focus:border-blue-400" />
                          </div>
                          <div class="lg:col-span-2">
                            <label class="mb-2 block text-sm font-medium text-gray-700 dark:text-gray-300">Bio / RT
                              Info</label>
                            <input type="text" v-model="form.bio" placeholder="Tell us about yourself"
                              class="w-full h-11 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20 dark:border-gray-700 dark:bg-gray-800 dark:text-white/90 dark:placeholder:text-white/50 dark:focus:border-blue-400" />
                          </div>
                        </div>
                      </div>
                    </div>

                    <div
                      class="flex items-center gap-3 px-2 border-t border-gray-200 dark:border-gray-700 pt-6 lg:justify-end">
                      <button @click="isProfileInfoModal = false" type="button"
                        class="flex justify-center rounded-lg border border-gray-300 bg-white px-6 py-2.5 text-sm font-medium text-gray-700 hover:bg-gray-50 transition-colors dark:border-gray-600 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700">
                        Close
                      </button>
                      <button type="submit"
                        class="flex justify-center rounded-lg bg-blue-600 px-6 py-2.5 text-sm font-medium text-white hover:bg-blue-700 transition-colors">
                        Save Changes
                      </button>
                    </div>
                  </form>
                </div>
              </div>
            </Transition>
          </div>
        </Transition>
      </Teleport>

      <Teleport to="body">
        <Transition name="fade">
          <div v-if="isChangePasswordModal" class="fixed inset-0 z-40 bg-black/50 flex items-center justify-center p-4">
            <Transition name="scale">
              <div class="relative w-full max-w-md rounded-3xl bg-white dark:bg-gray-900 shadow-2xl p-6 lg:p-8"
                v-if="isChangePasswordModal">
                <div class="flex items-center justify-between mb-6">
                  <h3 class="text-xl font-semibold text-gray-800 dark:text-white">Change Password</h3>
                  <button @click="isChangePasswordModal = false"
                    class="text-gray-400 hover:text-gray-600 dark:hover:text-gray-300 transition-colors">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12">
                      </path>
                    </svg>
                  </button>
                </div>

                <form @submit.prevent="submitPassword" class="space-y-5">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Current
                      Password</label>
                    <input type="password" v-model="passwordForm.oldPassword" required
                      placeholder="Enter current password"
                      class="w-full px-4 py-2.5 border border-gray-300 rounded-lg dark:bg-gray-800 dark:border-gray-700 dark:text-white placeholder:text-gray-400 dark:placeholder:text-white/50 focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">New Password</label>
                    <input type="password" v-model="passwordForm.newPassword" required placeholder="Enter new password"
                      class="w-full px-4 py-2.5 border border-gray-300 rounded-lg dark:bg-gray-800 dark:border-gray-700 dark:text-white placeholder:text-gray-400 dark:placeholder:text-white/50 focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Confirm
                      Password</label>
                    <input type="password" v-model="passwordForm.confirmPassword" required
                      placeholder="Confirm new password"
                      class="w-full px-4 py-2.5 border border-gray-300 rounded-lg dark:bg-gray-800 dark:border-gray-700 dark:text-white placeholder:text-gray-400 dark:placeholder:text-white/50 focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 outline-none transition" />
                  </div>

                  <div class="flex justify-end gap-3 mt-7 pt-4 border-t border-gray-200 dark:border-gray-700">
                    <button type="button" @click="isChangePasswordModal = false"
                      class="px-5 py-2.5 text-gray-700 bg-gray-100 rounded-lg hover:bg-gray-200 transition-colors dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700 font-medium">
                      Cancel
                    </button>
                    <button type="submit"
                      class="px-5 py-2.5 text-white bg-blue-600 rounded-lg hover:bg-blue-700 transition-colors font-medium">
                      Save Password
                    </button>
                  </div>
                </form>
              </div>
            </Transition>
          </div>
        </Transition>
      </Teleport>
    </div>
  </AdminLayout>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import AdminLayout from '@/components/layout/AdminLayout.vue'

const isProfileInfoModal = ref(false)
const isChangePasswordModal = ref(false)

const user = ref({
  firstName: '',
  lastName: '',
  email: '',
  phone: '',
  bio: '',
  role: '',
  facebook: '',
  twitter: '',
  linkedin: '',
  instagram: ''
})

const form = reactive({
  firstName: '',
  lastName: '',
  email: '',
  phone: '',
  bio: '',
  facebook: '',
  twitter: '',
  linkedin: '',
  instagram: ''
})

const passwordForm = reactive({
  oldPassword: '',
  newPassword: '',
  confirmPassword: ''
})

const fetchUserProfile = async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await axios.get('https://alentest.my.id/laporan/api/auth/me', {
      headers: { Authorization: `Bearer ${token}` }
    })

    if (res.data.status === 'success') {
      const data = res.data.data
      user.value = {
        firstName: data.username,
        lastName: '',
        email: '',
        phone: data.phone,
        bio: `RT ${data.rt}`,
        role: data.role,
        facebook: '',
        twitter: '',
        linkedin: '',
        instagram: ''
      }
    }
  } catch (error) {
    console.error("Gagal mengambil data profile", error)
  }
}

const openEditModal = () => {
  Object.assign(form, user.value)
  isProfileInfoModal.value = true
}

const saveProfile = () => {
  Object.assign(user.value, form)
  isProfileInfoModal.value = false

  Swal.fire({
    icon: 'success',
    title: 'Success',
    text: 'Profile updated locally (No API for update yet)',
    confirmButtonColor: '#2563eb',
    didOpen: (modal) => {
      modal.style.zIndex = '9999'
    }
  })
}

const submitPassword = async () => {
  if (passwordForm.newPassword !== passwordForm.confirmPassword) {
    Swal.fire({
      icon: 'error',
      title: 'Error',
      text: 'Password confirmation does not match',
      confirmButtonColor: '#dc2626',
      didOpen: (modal) => {
        modal.style.zIndex = '9999'
      }
    })
    return
  }

  try {
    const token = localStorage.getItem('token')
    await axios.put('https://alentest.my.id/laporan/api/auth/change-password', {
      oldPassword: passwordForm.oldPassword,
      newPassword: passwordForm.newPassword
    }, {
      headers: { Authorization: `Bearer ${token}` }
    })

    Swal.fire({
      icon: 'success',
      title: 'Success',
      text: 'Password changed successfully',
      confirmButtonColor: '#2563eb',
      didOpen: (modal) => {
        modal.style.zIndex = '9999'
      }
    })

    isChangePasswordModal.value = false
    passwordForm.oldPassword = ''
    passwordForm.newPassword = ''
    passwordForm.confirmPassword = ''
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: 'Failed',
      text: error.response?.data?.message || 'An error occurred while changing password',
      confirmButtonColor: '#dc2626',
      didOpen: (modal) => {
        modal.style.zIndex = '9999'
      }
    })
  }
}

onMounted(() => {
  fetchUserProfile()
})
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.scale-enter-active,
.scale-leave-active {
  transition: all 0.2s ease;
}

.scale-enter-from,
.scale-leave-to {
  opacity: 0;
  transform: scale(0.95);
}
</style>