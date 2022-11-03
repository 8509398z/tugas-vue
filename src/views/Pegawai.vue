<script setup>
import { NCard, NDataTable, NButton, NSpace, NModal, NForm, NFormItem, NInput } from 'naive-ui';
import { h, ref, onMounted, computed } from 'vue';
import axios from 'axios'

const data = ref([])
const currentPage = ref(1)
const isLoading = ref(false)
const totalPage = ref(1)
const perPage = 10

const editId = ref(null)
const formData = ref({
  nama: '',
  usia: 0,
  jabatan: '',
  tanggal_masuk: '01/01/1900',
  jenis_kelamin: ''
})

const showModal = ref(false)

const deleteLoadingId = ref(null)

const onEditPegawai = (pegawai) => {
  editId.value = pegawai.id
  formData.value.nama = pegawai.nama
  formData.value.usia = pegawai.usia
  formData.value.jabatan = pegawai.jabatan
  formData.value.tanggal_masuk = pegawai.tanggal_masuk
  formData.value.jenis_kelamin = pegawai.jenis_kelamin
  showModal.value = true
}

const resetForm = () => {
  editId.value = null
  formData.value.nama = ''
  formData.value.usia = 0
  formData.value.jabatan = 0
  formData.value.tanggal_masuk = 0
  formData.value.jenis_kelamin = 0
  showModal.value = false
}

const onSubmitForm = () => {
  if (editId.value) {
    editPegawai()
  } else {
    insertPegawai()
  }
}

const onDeletePegawai = (pegawai) => {
  if (!deleteLoadingId.value) {
    deletePegawai(pegawai.id)
  }
}

const insertPegawai = () => {
  axios({
    method: 'post',
    baseURL: 'http://localhost:3000',
    url: '/pegawai',
    data: formData.value
  }).then((response) => {
    resetForm()
    getPegawai(1)
  }).catch((error) => {
    console.log(error.message)
  })
}
const editPegawai = () => {
  axios({
    method: 'patch',
    baseURL: 'http://localhost:3000',
    url: '/pegawai/' + editId.value,
    data: formData.value
  }).then((response) => {
    resetForm()
    getPegawai(currentPage.value)
  }).catch((error) => {
    console.log(error.message)
  })
}
const deletePegawai = (id) => {
  deleteLoadingId.value = id
  axios({
    method: 'delete',
    baseURL: 'http://localhost:3000',
    url: '/pegawai/' + id
  }).then((response) => {
    getPegawai(currentPage.value)
  }).catch((error) => {
    console.log(error.message)
  }).finally(() => {
    deleteLoadingId.value = null
  })
}
const getPegawai = (page) => {
  currentPage.value = page
  isLoading.value = true
  axios({
    method: 'get',
    baseURL: 'http://localhost:3000',
    url: '/pegawai',
    params: {
      _page: page,
      _limit: perPage,
      _sort: 'id',
      _order: 'asc'
    }
  }).then((response) => {
    totalPage.value = Math.ceil(response.headers['x-total-count'] / perPage)
    data.value = response.data
  }).catch((error) => {
    console.log(error.message)
  }).finally(() => {
    isLoading.value = false
  })
}

onMounted(() => {
  getPegawai(1)
})

const columns = [
  { title: "No", key: 'id', size: 2 },
  { title: "Nama", key: 'nama' },
  { title: "Usia", key: 'usia' },
  { title: "Jabatan", key: 'jabatan' },
  { title: "Tanggal Masuk", key: 'tanggal_masuk' },
  { title: "Jenis Kelamin", key: 'jenis_kelamin' },
  {
    title: "Action",
    key: 'action',
    render: (row) => {
      return h(NSpace, {}, [
        h(
          NButton,
          {
            type: 'info',
            ghost: true,
            onClick: () => onEditPegawai(row)
          },
          { default: () => 'Edit' }
        ),
        h(
          NButton,
          {
            type: 'error',
            ghost: true,
            onClick: () => onDeletePegawai(row),
            loading: row.id == deleteLoadingId.value
          },
          { default: () => 'Delete' }
        )
      ])
    }
  },
]

const pagination = computed(() => {
  return {
    pageSize: perPage,
    pageCount: totalPage.value,
    page: currentPage.value
  }
})

const handlePageChange = (page) => {
  getPegawai(page)
}
</script>

<template>
  <NCard title="Pegawai">
    <NButton @click="showModal = true">Add New</NButton>
    <NDataTable 
      remote
      :loading="isLoading"
      :data="data"
      :columns="columns"
      :pagination="pagination"
      @update:page="handlePageChange" />
  </NCard>
  <NModal v-model:show="showModal">
    <NCard style="width: 50%" title="Data Pegawai">
      <NForm
        @submit.prevent="onSubmitForm"
        :model="formData"
        label-placement="left"
        label-width="auto"
        require-mark-placement="right-hanging"
        size="medium">
        <NFormItem label="Nama" path="nama">
          <NInput v-model:value="formData.nama" />
        </NFormItem>
        <NFormItem label="Usia" path="usia">
          <NInput v-model:value.number="formData.usia" />
        </NFormItem>
        <NFormItem label="Jabatan" path="jabatan">
          <NInput v-model:value="formData.jabatan" />
        </NFormItem>
        <NFormItem label="Tanggal Masuk" path="tanggal_masuk">
          <NInput v-model:value.date="formData.tanggal_masuk" />
        </NFormItem>
        <NFormItem label="Jenis Kelamin" path="jenis_kelamin">
          <NInput v-model:value.date="formData.jenis_kelamin" />
        </NFormItem>
        <NFormItem label="&nbsp;">
          <NButton type="info" ghost attr-type="submits">Save</NButton>
        </NFormItem>
      </NForm>
    </NCard>
  </NModal>
</template>