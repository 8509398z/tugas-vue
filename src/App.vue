<script setup>
import { RouterView } from 'vue-router';
import {
  NLayout,
  NLayoutContent
} from 'naive-ui';

import Sidebar from './layouts/Sidebar.vue';
import Footer from './layouts/Footer.vue';
import HeaderTop from './layouts/Headertop.vue';
</script>

<template>
  <HeaderTop />
  <NLayout has-sider>
    <Sidebar />
    <NLayout style="min-height: 100vh;">
      <NLayoutContent 
        content-style="padding: 24px; padding-bottom: 100px">
        <RouterView />
      </NLayoutContent>
      <Footer />
    </NLayout>
  </NLayout>
</template>