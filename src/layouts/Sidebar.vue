<script setup>
import { NLayoutSider, NMenu } from 'naive-ui'
import { h } from 'vue';
import { RouterLink } from 'vue-router';

const menus = [
  { title: 'Home', to: '/' },
  { title: 'Pegawai', to: '/pegawai' },
]

const menuOptions = menus.map((menu) => {
  return {
    label: () => h(
      RouterLink,
      { to: menu.to },
      { default: () => menu.title }
    ),
    key: menu.to,
    icon: null
  }
})

</script>

<template>
  <NLayoutSider
    bordered
    show-trigger="arrow-circle">
    <NMenu :options="menuOptions" />
  </NLayoutSider>
</template>
