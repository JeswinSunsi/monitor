<template>
          <footer class="footer">
        <nav class="navigation">
          <a v-for="navItem in navigationItems" :key="navItem.name"
            :class="['nav-item', navItem.active ? 'nav-item--active' : '']" href="#"
            @click.prevent="navigateTo(navItem.name)">
            <span :class="['material-symbols-outlined', !navItem.active && 'nav-icon--outlined']">{{ navItem.icon
              }}</span>
            <p class="nav-label">{{ navItem.name }}</p>
          </a>
        </nav>
      </footer>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { ref } from 'vue';

const router = useRouter();

const navigationItems = ref([
  { name: 'Home', icon: 'home', active: true, route: '/' },
  { name: 'OD/ML', icon: 'calendar_month', active: false, route: '/odml' },
  { name: 'Stats', icon: 'bar_chart', active: false, route: '/stats' },
  { name: 'Profile', icon: 'person', active: false, route: '/profile' }
])

const navigateTo = (section) => {
  navigationItems.value.forEach(item => {
    item.active = item.name === section
    if (item.active) {
      router.push(item.route)
      console.log(`Mapped ${section} to ${item.route}`)
    }
  })
}
</script>

<style scoped>
.material-symbols-outlined {
  font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}

.nav-icon--outlined {
  font-variation-settings: 'FILL' 0, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}

.footer {
  position: fixed;
  bottom: 0;
  width: 100%;
  background-color: white;
  border-top: 1px solid #e5e7eb;
  z-index: 10;
  box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.05);
}

.navigation {
  display: flex;
  justify-content: space-around;
  padding: 12px 0;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  gap: 4px;
  width: 25%;
  text-decoration: none;
  color: #6b7280;
}

.nav-label {
  font-size: 12px;
  font-weight: 500;
  margin: 0;
}

.nav-item--active .nav-label {
  font-weight: 700;
}


.nav-item--active {
  color: #4f46e5;
}
</style>