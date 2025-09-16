<template>
  <div class="app-container">
    <div class="main-wrapper">
      <div class="content-wrapper">
        <!-- Header -->
        <header class="header">
          <div class="header-background">
            <img alt="University campus" class="header-bg-image"
              src="https://images.unsplash.com/photo-1523580846011-d3a5bc25702b?q=80&w=1200" />
            <div class="header-gradient"></div>
          </div>
          <div class="header-content">
            <div class="welcome-section">
              <p class="welcome-text">Welcome back,</p>
              <h1 class="user-name">Prof. {{ userName }}</h1>
            </div>
            <button @click="handleNotifications" class="notification-btn">
              <span class="material-symbols-outlined">notifications</span>
              <span v-if="hasNotifications" class="notification-badge"></span>
            </button>
          </div>
        </header>

        <!-- Main -->
        <main class="main-content">

          <!-- Start New Session -->
          <div class="qr-card">
            <div class="qr-card-content">
              <div class="qr-icon-container">
                <span class="material-symbols-outlined qr-icon">playlist_add_check_circle</span>
              </div>
              <h2 class="qr-title">Start a New Attendance Session</h2>
              <p class="qr-description">
                Begin tracking attendance for your current class. Students can check in using QR or Face Scan.
              </p>
              <button @click="startNewSession" class="qr-scan-btn">
                <span class="material-symbols-outlined">add_task</span>
                <span>Start Session</span>
              </button>
            </div>
          </div>

          <!-- Active Session -->
          <div v-if="activeSession" class="active-session">
            <h2>Ongoing Session: {{ activeSession.subject }}</h2>
            <p>{{ activeSession.time }}</p>
            <div class="live-attendance">
              <h3>Live Attendance</h3>
              <ul>
                <li v-for="student in liveStudents" :key="student.id">
                  ✅ {{ student.name }}
                </li>
                <li v-if="liveStudents.length === 0">No students checked in yet...</li>
              </ul>
            </div>
            <button @click="endSession" class="end-btn">
              <span class="material-symbols-outlined">stop_circle</span> End Session
            </button>
          </div>

          <div class="line-break" style="margin: 0 auto; width: 70%; background-color: lightgray; height: 1px;"></div>

          <!-- Attendance History -->
          <div class="schedule-container">
            <header class="schedule-header">
              <h2 class="schedule-title">Attendance History</h2>
            </header>

            <div class="schedule-body">
              <div v-if="history.length > 0">
                <div v-for="(session, index) in history" :key="index" class="schedule-item">
                  <div class="schedule-time-col">{{ session.date }}</div>
                  <div class="schedule-card schedule-card--history">
                    <p class="schedule-subject">{{ session.subject }}</p>
                    <p class="schedule-time-range">{{ session.time }}</p>
                    <p class="schedule-time-range">Present: {{ session.present }}/{{ session.total }}</p>
                    <button class="export-btn" @click="exportReport(session)">
                      <span class="material-symbols-outlined">download</span> Export
                    </button>
                  </div>
                </div>
              </div>
              <div v-else class="no-classes">
                <p>No past attendance sessions yet.</p>
              </div>
            </div>
          </div>
        </main>
      </div>
      <NavComp />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import NavComp from '../components/nav.vue'

const userName = ref('Jezwin')
const hasNotifications = ref(true)

// Active session state
const activeSession = ref(null)
const liveStudents = ref([])

// Attendance history
const history = ref([
  { date: 'Sep 10', subject: 'Physics', time: '10:00 - 11:30 AM', present: 25, total: 30 },
  { date: 'Sep 12', subject: 'Chemistry', time: '1:00 - 2:00 PM', present: 28, total: 30 },
])

const getUserDetails = () => {
  const storedData = localStorage.getItem("signupData")
  if (!storedData) return
  const formData = JSON.parse(storedData)
  userName.value = formData.fullName.split(" ")[0]
}

onMounted(() => {
  getUserDetails()
})

// Start new session
const startNewSession = () => {
  activeSession.value = {
    subject: 'Advanced Physics',
    time: 'Now - 1:00 PM'
  }
  liveStudents.value = []
  console.log("New session started")
}

// End session
const endSession = () => {
  history.value.unshift({
    date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
    subject: activeSession.value.subject,
    time: activeSession.value.time,
    present: liveStudents.value.length,
    total: 30
  })
  activeSession.value = null
  console.log("Session ended")
}

// Export report
const exportReport = (session) => {
  console.log("Exporting session:", session)
}

const handleNotifications = () => {
  console.log("Notifications clicked")
}
</script>

<style scoped>
.active-session {
  background-color: #eef2ff;
  border: 1px solid #c7d2fe;
  padding: 1rem;
  border-radius: 12px;
}
.live-attendance {
  margin: 1rem 0;
  background: white;
  padding: 1rem;
  border-radius: 8px;
}
.end-btn {
  background: #ef4444;
  color: white;
  padding: 0.6rem 1rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}
.export-btn {
  background: #4f46e5;
  color: white;
  border: none;
  padding: 0.3rem 0.6rem;
  border-radius: 6px;
  cursor: pointer;
  margin-top: 8px;
}

.active-session {
  background-color: #eef2ff;
  border: 1px solid #c7d2fe;
  padding: 1rem;
  border-radius: 12px;
  margin-top: 1.5rem;
}

.active-session h2 {
  font-size: 20px;
  font-weight: 700;
  margin: 0 0 0.5rem 0;
  color: #1e3a8a;
}

.live-attendance {
  margin: 1rem 0;
  background: white;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.05);
}

.live-attendance h3 {
  margin: 0 0 0.8rem 0;
  font-size: 16px;
  font-weight: 600;
  color: #374151;
}

.live-attendance ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.live-attendance li {
  padding: 6px 0;
  font-size: 14px;
  border-bottom: 1px solid #f3f4f6;
}

.end-btn {
  background: #ef4444;
  color: white;
  padding: 0.6rem 1rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 6px;
  margin-top: 1rem;
}

.end-btn:hover {
  background: #dc2626;
}

.export-btn {
  background: #4f46e5;
  color: white;
  border: none;
  padding: 0.3rem 0.6rem;
  border-radius: 6px;
  cursor: pointer;
  margin-top: 8px;
  font-size: 13px;
  display: inline-flex;
  align-items: center;
  gap: 4px;
}

.export-btn:hover {
  background: #4338ca;
}

.material-symbols-outlined {
  font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}

.nav-icon--outlined {
  font-variation-settings: 'FILL' 0, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}

* {
  box-sizing: border-box;
}

.app-container {
  background-color: white;
  font-family: Manrope, "Noto Sans", sans-serif;
}

.main-wrapper {
  position: relative;
  display: flex;
  min-height: 100vh;
  width: 100%;
  flex-direction: column;
  justify-content: space-between;
  overflow-x: hidden;
}

.content-wrapper {
  flex-grow: 1;
}

.header {
  position: relative;
  background-color: white;
  padding: 24px 18px 76px;
}

.header-background {
  position: absolute;
  inset: 0;
  overflow: hidden;
}

.header-bg-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0.1;
}

.header-gradient {
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, white, transparent);
}

.header-content {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.welcome-text {
  color: #6b7280;
  font-size: 14px;
  margin: 0;
  margin-bottom: 0.4rem;
}

.user-name {
  color: #111827;
  font-size: 30px;
  font-weight: 700;
  margin: 0;
}

.notification-btn {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 48px;
  width: 48px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.7);
  backdrop-filter: blur(8px);
  color: #4b5563;
  border: 1px solid rgba(229, 231, 235, 0.5);
  cursor: pointer;
}

.notification-badge {
  position: absolute;
  top: 8px;
  right: 8px;
  height: 12px;
  width: 12px;
  border-radius: 50%;
  background-color: #ef4444;
  border: 2px solid white;
}

.main-content {
  padding: 16px;
  padding-bottom: 6rem;
  margin-top: -64px;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.qr-card {
  z-index: 100;
  background: linear-gradient(135deg, #4f46e5, #6366f1);
  border-radius: 16px;
  padding: 24px;
  color: white;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

.qr-card-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.qr-icon-container {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  width: 56px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.2);
  margin-bottom: 12px;
}

.qr-icon {
  font-size: 26px !important;
  color: white;
}

.qr-title {
  line-height: 2rem;
  color: white;
  font-size: 22px;
  font-weight: 700;
  margin: 0 0 4px 0;
}

.qr-description {
  color: #c7d2fe;
  font-size: 14px;
  width: 80.5%;
  line-height: 18px;
  margin: 0 0 20px 0;
}

.qr-scan-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  height: 48px;
  padding: 0 24px;
  background-color: white;
  color: #4f46e5;
  font-size: 16px;
  font-weight: 700;
  border: none;
  border-radius: 12px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  width: 100%;
}

@media (min-width: 768px) {
  .qr-scan-btn {
    width: auto;
  }
}

body {
  margin: 0;
  padding: 0;
}

.schedule-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
}

.schedule-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 8px;
}

.schedule-title {
  font-size: 22px;
  font-weight: 700;
  color: #111827;
  margin: 0;
}

.schedule-nav {
  display: flex;
  gap: 16px;
  color: #6b7280;
}

.date-selector {
  display: flex;
  justify-content: space-between;
  text-align: center;
}

.date-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  padding: 8px;
  border-radius: 10px;
  cursor: pointer;
  width: 17%;
}

.date-day {
  margin-bottom: 10px;
  font-size: 14px;
  color: #6b7280;
  font-weight: 500;
}

.date-number {
  font-size: 16px;
  font-weight: 700;
  color: #111827;
}

.date-item--active {
  background-color: #4f46e5;
  color: white;
}

.date-item--active .date-day,
.date-item--active .date-number {
  color: white;
}

.schedule-body {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.schedule-item {
  display: flex;
  align-items: stretch;
  gap: 8px;
}

.schedule-time-col {
  font-size: 10px;
  color: #9ca3af;
  padding-top: 4px;
  padding-left: 0.5rem;
  line-height: 1rem;
  text-transform: uppercase;
  width: 60px;
  text-align: left;
  flex-shrink: 0;
}

.schedule-card {
  flex-grow: 1;
  background-color: #f9fafb;
  padding: 16px;
  border-left: 4px solid;
}

.schedule-subject {
  font-size: 16px;
  font-weight: 700;
  color: #111827;
  margin: 0 0 10px 0;
}

.schedule-time-range {
  font-size: 13px;
  color: #6b7280;
  margin: 0;
}

.date-selector {
  position: relative;
  display: flex;
  justify-content: space-between;
  margin: 1rem 0;
}

.active-date-indicator {
  position: absolute;
  top: 0;
  height: 100%;
  background-color: #4f46e5;
  color: #FFF;
  border-radius: 10px;
  transition: left 0.4s cubic-bezier(0.25, 0.8, 0.25, 1), width 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
  z-index: 1;
}

.date-item {
  cursor: pointer;
  z-index: 2;
  transition: color 0.3s ease-in-out;
  flex: 1;
  text-align: center;
  padding: 8px 4px;
  transition: color 0.2s ease-out;

}

.date-item--active-text {
  color: white !important;
  font-weight: bold;

}

.date-item--active-text .date-day,
.date-item--active-text .date-number {
  color: white !important;
  transition: color 0.1s ease-in 0.15s;

}

.no-classes {
  text-align: center;
  padding: 3rem 1rem;
  color: #888;
  background-color: #f9f9f9;
  border-radius: 12px;
  margin: 1rem 0;
}

.schedule-card--physics {
  border-color: #3b82f6;
  background-color: #eff6ff;
}

.schedule-card--music {
  border-color: #f97316;
  background-color: #fff7ed;
}

.schedule-card--lunch {
  border-color: #6b7280;
  background-color: #f3f4f6;
}

.schedule-card--art {
  border-color: #8b5cf6;
  background-color: #f5f3ff;
}

.schedule-card--history {
  border-color: #ec4899;
  background-color: #fdf2f8;
}
</style>
