<template>
  <div class="canvas">
    <!-- Background pattern -->
    <div class="grid-pattern"></div>

    <!-- Teleported containers -->
    <teleport to="body" defer>
      <div>
        <div
          v-for="box in containers"
          :key="box.id"
          class="container"
          :style="{
            left: box.x + 'px',
            top: box.y + 'px',
            width: box.w + 'px',
            height: box.h + 'px',
            zIndex: dragging?.id === box.id ? 1000 : 1
          }"
          @mousedown="startDrag($event, box)"
        >
          <header class="container-header">
            <span class="icon">{{ box.icon }}</span>
            <div class="text">
              <h2>{{ box.title }}</h2>
              <p>{{ box.subtitle }}</p>
            </div>
          </header>

          <div class="container-body">
            <div
              v-if="['Tasks', 'Files', 'Analytics'].includes(box.title)"
              class="progress"
            >
              <div class="progress-bar" :style="{ width: box.progress + '%' }"></div>
            </div>
          </div>
        </div>
      </div>
    </teleport>
  </div>
</template>

<script setup>
import { ref } from "vue";

const containers = ref([
  { id: 1, title: "Tasks", subtitle: "8 items pending", x: 80, y: 120, w: 260, h: 180, icon: "📋", progress: 60 },
  { id: 2, title: "Analytics", subtitle: "Revenue insights", x: 420, y: 80, w: 320, h: 220, icon: "📊", progress: 75 },
  { id: 3, title: "Messages", subtitle: "3 unread", x: 820, y: 200, w: 240, h: 160, icon: "💬" },
  { id: 4, title: "Calendar", subtitle: "Today's schedule", x: 200, y: 400, w: 280, h: 140, icon: "📅" },
  { id: 5, title: "Files", subtitle: "24 documents", x: 580, y: 380, w: 200, h: 200, icon: "📁", progress: 45 },
  { id: 6, title: "Settings", subtitle: "System config", x: 860, y: 450, w: 220, h: 120, icon: "⚙️" },
  { id: 7, title: "Music", subtitle: "Now playing", x: 120, y: 650, w: 300, h: 100, icon: "🎵" },
  { id: 8, title: "Weather", subtitle: "22°C Sunny", x: 500, y: 620, w: 180, h: 140, icon: "☀️" }
]);

const dragging = ref(null);
const offset = ref({ x: 0, y: 0 });

function startDrag(e, box) {
  dragging.value = box;
  offset.value = { x: e.clientX - box.x, y: e.clientY - box.y };
  window.addEventListener("mousemove", onDrag);
  window.addEventListener("mouseup", stopDrag);
}

function onDrag(e) {
  if (!dragging.value) return;
  dragging.value.x = e.clientX - offset.value.x;
  dragging.value.y = e.clientY - offset.value.y;
}

function stopDrag() {
  dragging.value = null;
  window.removeEventListener("mousemove", onDrag);
  window.removeEventListener("mouseup", stopDrag);
}
</script>

<style scoped>
.canvas {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(to bottom right, #f9fafb, #f3f4f6);
  overflow: hidden;
}

.grid-pattern {
  position: absolute;
  inset: 0;
  background-image: radial-gradient(circle, rgba(0, 0, 0, 0.05) 1px, transparent 1px);
  background-size: 40px 40px;
  pointer-events: none;
}

.container {
  position: absolute;
  background: white;
  border-radius: 1rem;
  box-shadow: 0 6px 14px rgba(0, 0, 0, 0.08);
  border: 1px solid #e5e7eb;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  cursor: grab;
  transition: box-shadow 0.2s ease, transform 0.2s ease;
}

.container:active {
  cursor: grabbing;
  transform: scale(1.02);
  box-shadow: 0 12px 28px rgba(0, 0, 0, 0.12);
}

.container-header {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 0.5rem;
}

.icon {
  font-size: 1.5rem;
}

.container-header h2 {
  font-size: 1rem;
  font-weight: 600;
  margin: 0;
  color: #111827;
}

.container-header p {
  font-size: 0.875rem;
  color: #6b7280;
  margin: 0;
}

.container-body {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}

.progress {
  width: 100%;
  height: 6px;
  background: #e5e7eb;
  border-radius: 9999px;
  overflow: hidden;
}

.progress-bar {
  height: 100%;
  background: #3b82f6;
  transition: width 0.3s ease;
}
</style>
