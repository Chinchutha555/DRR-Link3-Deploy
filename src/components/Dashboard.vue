<script setup>
import { ref, computed } from "vue";
import ProjectMap from "../components/Map.vue";

const selectedTab = ref("phase1");

const dashboardData = computed(() => {
  if (selectedTab.value === "phase2") {
    return {
      routeCount: 28,
      routeLabel: "สายทางในระยะ Phase 2",
      distance: "1,245.80",
    };
  }

  return {
    routeCount: 18,
    routeLabel: "สายทางในระยะ Phase 1",
    distance: "842.35",
  };
});
</script>

<template>
  <div class="untree_co-section" id="pricing-section" style="padding-top: 0">
    <h3
      class="heading text-center"
      data-aos="fade-up"
      style="margin-bottom: 40px"
    >
      ความคืบหน้าโครงการ
    </h3>

    <div class="tabs">
      <button
        type="button"
        :class="['tab-button', { 'active-tab': selectedTab === 'phase1' }]"
        @click="selectedTab = 'phase1'"
      >
        Phase 1
      </button>

      <button
        type="button"
        :class="['tab-button', { 'active-tab': selectedTab === 'phase2' }]"
        @click="selectedTab = 'phase2'"
      >
        Phase 2
      </button>
    </div>

    <div class="container">
      <!-- 🔷 แถวบน -->
      <div class="top-row">
        <div class="box stat-box">
          <h3>จำนวนสายทาง</h3>
          <p class="stat-value">{{ dashboardData.routeCount }}</p>
          <!-- <p class="stat-label">{{ dashboardData.routeLabel }}</p> -->
        </div>

        <div class="box stat-box">
          <h3>ระยะทางรวม</h3>
          <p class="stat-value">
            {{ dashboardData.distance }}
            <span class="stat-label">กิโลเมตร</span>
          </p>
        </div>
      </div>

      <!-- 🔷 แถวล่าง -->
      <div class="bottom-row">
        <div class="box full-width">
          <ProjectMap :phase="selectedTab" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-wrap: wrap;
  padding: 20px;
  padding-left: 0;
  padding-right: 0;
  gap: 0;
}

.tabs {
  display: flex;
  justify-content: center;
  gap: 14px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.tab-button {
  border: 1px solid #407bff;
  background-color: #ffffff;
  color: #407bff;
  padding: 10px 22px;
  border-radius: 999px;
  cursor: pointer;
  transition: all 0.25s ease;
  font-weight: 600;
}

.tab-button:hover {
  transform: translateY(-2px);
  border-color: #93c5fd;
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.08);
}

.tab-button.active-tab {
  background-color: #407bff;
  color: #ffffff;
}

.left-section {
  width: 66.66%;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.right-section {
  width: 33.33%;
  padding: 20px;
  display: flex;
  flex-direction: column;
}

.box {
  background-color: #ffffff;
  padding: 24px;
  border: 1px solid #dbe3ef;
  border-radius: 18px;
  color: #0f172a;
  transition: all 0.25s ease;
}

.full-height {
  flex-grow: 1;
}

.map-card {
  height: 100%;
}

.heading {
  color: #0f172a;
  font-weight: 800;
}

@media screen and (max-width: 992px) {
  .left-section,
  .right-section {
    width: 100%;
  }
}

@media screen and (max-width: 768px) {
  .container {
    padding: 12px;
  }

  .left-section,
  .right-section {
    padding: 12px;
  }

  .stat-value {
    font-size: 32px;
  }
}

/* 🔷 แถวบน */
.top-row {
  width: 100%;
  display: flex;
  gap: 20px;
  margin-bottom: 20px;
}

/* ให้ 2 กล่องเท่ากัน */
.top-row .box {
  width: 50%;
}

/* 🔷 แถวล่าง */
.bottom-row {
  width: 100%;
}

.stat-box {
  display: flex;
  flex-direction: column;
  justify-content: center;

}

.stat-box h3 {
  margin: 0 0 16px;
  font-size: 22px;
  font-weight: 700;
  color: #1e293b;
}

.stat-value {
  margin: 0;
  font-size: 40px;
  font-weight: 800;
  color: #2563eb;
  line-height: 1.1;
}

.stat-label {
  margin-top: 10px;
  color: #64748b;
  font-size: 15px;
}


</style>
