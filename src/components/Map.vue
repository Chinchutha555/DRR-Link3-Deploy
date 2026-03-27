<script setup>
import {
  ref,
  computed,
  nextTick,
  onMounted,
  onBeforeUnmount,
  watch,
} from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import {
  LMap,
  LTileLayer,
  LMarker,
  LPopup,
  LGeoJson,
} from "@vue-leaflet/vue-leaflet";
import routesData from "../data/routes.json";
import pointsData from "../data/points.json";

const props = defineProps({
  phase: {
    type: String,
    default: "phase1",
  },
});

const mapRef = ref(null);
const mapInstance = ref(null);
const mapReady = ref(false);

const zoom = ref(6);
const center = ref([16.5, 101.0]);
const selectedRegion = ref("all");

const regionOptions = [
  { key: "all", label: "ทั้งหมด" },
  { key: "north", label: "ภาคเหนือ" },
  { key: "northeast", label: "ภาคอีสาน" },
];

const regionNameMap = {
  all: "ทั้งหมด",
  north: "ภาคเหนือ",
  northeast: "ภาคอีสาน",
};

const regionViewConfig = {
  all: {
    center: [16.5, 101.0],
    zoom: 6,
  },
  north: {
    center: [17.8, 99.2],
    zoom: 7,
  },
  northeast: {
    center: [16.0, 103.0],
    zoom: 7,
  },
};

const phaseLabel = computed(() => {
  return props.phase === "phase2" ? "Phase 2" : "Phase 1";
});

const regionLabel = computed(() => {
  return regionNameMap[selectedRegion.value];
});

const currentPoints = computed(() => {
  return pointsData.filter((item) => item.phase === props.phase);
});

const currentLines = computed(() => {
  return routesData.features.filter((feature) => {
    return feature.properties.phase === props.phase;
  });
});

const filteredPoints = computed(() => {
  if (selectedRegion.value === "all") return currentPoints.value;
  return currentPoints.value.filter(
    (item) => item.region === selectedRegion.value
  );
});

const filteredLines = computed(() => {
  if (selectedRegion.value === "all") return currentLines.value;
  return currentLines.value.filter(
    (item) => item.properties.region === selectedRegion.value
  );
});

const geoJsonCollection = computed(() => ({
  type: "FeatureCollection",
  features: filteredLines.value,
}));

function styleGeoJson(feature) {
  const region = feature.properties.region;

  if (region === "north") {
    return {
      color: "#16a34a",
      weight: 5,
    };
  }

  if (region === "northeast") {
    return {
      color: "#2563eb",
      weight: 5,
    };
  }

  return {
    color: "#64748b",
    weight: 4,
  };
}

function getBounds(regionKey = "all") {
  const bounds = L.latLngBounds([]);

  currentPoints.value
    .filter((item) => regionKey === "all" || item.region === regionKey)
    .forEach((item) => {
      bounds.extend([item.lat, item.lng]);
    });

  currentLines.value
    .filter(
      (feature) => regionKey === "all" || feature.properties.region === regionKey
    )
    .forEach((feature) => {
      const layer = L.geoJSON(feature);
      const layerBounds = layer.getBounds();
      if (layerBounds.isValid()) {
        bounds.extend(layerBounds);
      }
    });

  return bounds;
}

function refreshMapSize() {
  if (!mapInstance.value) return;

  setTimeout(() => {
    mapInstance.value.invalidateSize();
  }, 250);
}

function onMapReady(map) {
  mapInstance.value = map;

  setTimeout(() => {
    if (!mapInstance.value) return;

    mapInstance.value.invalidateSize();

    const bounds = getBounds("all");
    if (bounds.isValid()) {
      mapInstance.value.fitBounds(bounds, { padding: [40, 40] });
    }
  }, 300);
}

async function zoomToRegion(regionKey) {
  selectedRegion.value = regionKey;
  await nextTick();

  if (!mapInstance.value) return;

  mapInstance.value.invalidateSize();

  if (regionKey === "all") {
    const bounds = getBounds("all");
    if (bounds.isValid()) {
      mapInstance.value.fitBounds(bounds, { padding: [48, 48] });
    }
    return;
  }

  const view = regionViewConfig[regionKey];
  if (view) {
    mapInstance.value.flyTo(view.center, view.zoom, {
      animate: true,
      duration: 1.2,
    });
  }
}

watch(
  () => props.phase,
  async () => {
    selectedRegion.value = "all";
    await nextTick();

    if (!mapInstance.value) return;

    mapInstance.value.invalidateSize();

    const bounds = getBounds("all");
    if (bounds.isValid()) {
      mapInstance.value.fitBounds(bounds, { padding: [40, 40] });
    }
  }
);

onMounted(() => {
  setTimeout(() => {
    mapReady.value = true;
  }, 200);

  window.addEventListener("resize", refreshMapSize);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", refreshMapSize);
});
</script>

<template>
  <div class="project-map">
    <div class="map-header">
      <h3 class="map-title">แผนที่โครงการ</h3>

      <div class="map-toolbar">
        <button
          v-for="item in regionOptions"
          :key="item.key"
          type="button"
          class="region-btn"
          :class="{ active: selectedRegion === item.key }"
          @click="zoomToRegion(item.key)"
        >
          {{ item.label }}
        </button>
      </div>
    </div>

    <div class="map-subtitle">
      <span>ข้อมูลที่แสดง:</span>
      <strong>{{ phaseLabel }}</strong>
      <span class="divider">|</span>
      <span>พื้นที่:</span>
      <strong>{{ regionLabel }}</strong>
    </div>

    <div class="map-wrapper">
      <l-map
        v-if="mapReady"
        ref="mapRef"
        v-model:zoom="zoom"
        :center="center"
        :use-global-leaflet="false"
        @ready="onMapReady"
      >
        <l-tile-layer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
          layer-type="base"
          name="OpenStreetMap"
        />

        <l-marker
          v-for="item in filteredPoints"
          :key="item.id"
          :lat-lng="[item.lat, item.lng]"
        >
          <l-popup>
            <div class="popup-content">
              <strong>{{ item.name }}</strong><br />
              จังหวัด: {{ item.province }}<br />
              ระยะ: {{ item.phaseText }}<br />
              ภูมิภาค: {{ item.regionText }}
            </div>
          </l-popup>
        </l-marker>

        <l-geo-json
          v-if="filteredLines.length"
          :geojson="geoJsonCollection"
          :options-style="styleGeoJson"
        />
      </l-map>
    </div>
  </div>
</template>

<style scoped>
.project-map {
  display: flex;
  flex-direction: column;
  gap: 12px;
  height: 100%;
}

.map-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.map-title {
  margin: 0;
  font-size: 22px;
  font-weight: 700;
  color: #1e293b;
  white-space: nowrap;
}

.map-toolbar {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
  flex-wrap: wrap;
}

.region-btn {
  border: 1px solid #dbe3ef;
  background: #ffffff;
  color: #334155;
  padding: 8px 14px;
  border-radius: 999px;
  cursor: pointer;
  transition: all 0.25s ease;
  font-size: 14px;
  font-weight: 500;
}

.region-btn:hover {
  transform: translateY(-2px);
  border-color: #93c5fd;
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.08);
}

.region-btn.active {
  background: #407bff;
  color: #ffffff;
  border-color: #407bff;
}

.map-subtitle {
  font-size: 14px;
  color: #475569;
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  align-items: center;
  margin-top: 0;
}

.divider {
  color: #cbd5e1;
}

.map-wrapper {
  width: 100%;
  height: 520px;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid #e2e8f0;
  background: #ffffff;
}

.popup-content {
  line-height: 1.6;
  min-width: 180px;
}

:deep(.leaflet-container) {
  width: 100%;
  height: 100%;
  z-index: 1;
}

@media screen and (max-width: 768px) {
  .map-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .map-toolbar {
    width: 100%;
    justify-content: flex-start;
  }

  .map-wrapper {
    height: 400px;
  }
}
</style>