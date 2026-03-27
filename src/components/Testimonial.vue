<script setup>
import { ref, computed } from "vue";
import { themeColor } from "../data/items";

const heading = "ล่าสุด";
const subHeading = "ข่าวประชาสัมพันธ์";

const blogItems = [
  {
    title: "การประชุมชี้แจงแผนงานโครงการ",
    name: "ฝ่ายโครงการ",
    date: "25 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_1.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 1 สามารถใส่ข้อความยาว ๆ ได้ที่นี่ เช่น วัน เวลา สถานที่ และสาระสำคัญของกิจกรรม",
  },
  {
    title: "การลงพื้นที่สำรวจภาคสนาม",
    name: "ฝ่ายสำรวจ",
    date: "20 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_2.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 2 สำหรับแสดงใน popup เมื่อผู้ใช้คลิกที่กล่องข่าว",
  },
  {
    title: "สรุปผลการประชุมรับฟังความคิดเห็น",
    name: "ฝ่ายวิชาการ",
    date: "15 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_3.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 3 พร้อมข้อมูลสรุปประเด็นสำคัญจากการประชุมรับฟังความคิดเห็น",
  },
  {
    title: "กิจกรรมลงพื้นที่ติดตามความก้าวหน้า",
    name: "ฝ่ายติดตาม",
    date: "10 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_3.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 4 ใช้สำหรับทดสอบการเลื่อนการ์ดด้วยปุ่มซ้ายขวา",
  },
  {
    title: "กิจกรรมลงพื้นที่ติดตามความก้าวหน้า",
    name: "ฝ่ายติดตาม",
    date: "10 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_3.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 4 ใช้สำหรับทดสอบการเลื่อนการ์ดด้วยปุ่มซ้ายขวา",
  },
  {
    title: "กิจกรรมลงพื้นที่ติดตามความก้าวหน้า",
    name: "ฝ่ายติดตาม",
    date: "10 มี.ค. 2569",
    category: "ข่าวประชาสัมพันธ์",
    photo: "images/person_3.jpg",
    detail:
      "รายละเอียดเพิ่มเติมของข่าวประชาสัมพันธ์รายการที่ 4 ใช้สำหรับทดสอบการเลื่อนการ์ดด้วยปุ่มซ้ายขวา",
  },
];

const showModal = ref(false);
const selectedItem = ref(null);
const slider = ref(null);

const openModal = (item) => {
  selectedItem.value = item;
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
  selectedItem.value = null;
};

const currentIndex = ref(0);

const itemsPerPage = 3;

// จำนวนหน้า (dot)
const totalPages = computed(() => Math.ceil(blogItems.length / itemsPerPage));

const isAtStart = computed(() => currentIndex.value === 0);

const isAtEnd = computed(() => currentIndex.value === totalPages.value - 1);

const next = () => {
  if (isAtEnd.value) return; // กันกดตอนสุด
  currentIndex.value++;
  scrollToCurrent();
};

const prev = () => {
  if (isAtStart.value) return;
  currentIndex.value--;
  scrollToCurrent();
};

const goTo = (index) => {
  currentIndex.value = index;
  scrollToCurrent();
};

const scrollToCurrent = () => {
  const width = slider.value.clientWidth;
  slider.value.scrollTo({
    left: width * currentIndex.value,
    behavior: "smooth",
  });
};
</script>

<template>
  <div class="untree_co-section" id="testimonials-section">
    <div class="container">
      <div class="row">
        <div class="col-12 mb-4" data-aos="fade-up" data-aos-delay="300">
          <div class="d-flex justify-content-between align-items-center">
            <div class="text-left">
              <span class="caption" :style="[{ color: themeColor }]">
                {{ heading }}
              </span>
              <h2 class="heading mb-0">{{ subHeading }}</h2>
            </div>

            <div class="d-flex align-items-center">
              <a
                href="#"
                class="custom-prev"
                :class="{ disabled: isAtStart }"
                @click.prevent="prev"
              >
                <span>
                  <svg
                    class="bi bi-arrow-left"
                    width="1em"
                    height="1em"
                    viewBox="0 0 16 16"
                    fill="currentColor"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M5.854 4.646a.5.5 0 0 1 0 .708L3.207 8l2.647 2.646a.5.5 0 0 1-.708.708l-3-3a.5.5 0 0 1 0-.708l3-3a.5.5 0 0 1 .708 0z"
                    />
                    <path
                      fill-rule="evenodd"
                      d="M2.5 8a.5.5 0 0 1 .5-.5h10.5a.5.5 0 0 1 0 1H3a.5.5 0 0 1-.5-.5z"
                    />
                  </svg>
                </span>
              </a>
&nbsp;&nbsp;&nbsp;
              <a
                href="#"
                class="custom-next"
                :class="{ disabled: isAtEnd }"
                @click.prevent="next"
              >
                <span>
                  <svg
                    class="bi bi-arrow-right"
                    width="1em"
                    height="1em"
                    viewBox="0 0 16 16"
                    fill="currentColor"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M10.146 4.646a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708-.708L12.793 8l-2.647-2.646a.5.5 0 0 1 0-.708z"
                    />
                    <path
                      fill-rule="evenodd"
                      d="M2 8a.5.5 0 0 1 .5-.5H13a.5.5 0 0 1 0 1H2.5A.5.5 0 0 1 2 8z"
                    />
                  </svg>
                </span>
              </a>
            </div>
          </div>
        </div>
      </div>

      <div class="row">
        <div class="col-12" data-aos="fade-up" data-aos-delay="500">
          <div ref="slider" class="news-slider">
            <div
              v-for="(item, index) in blogItems"
              :key="index"
              class="news-slide"
            >
              <div
                class="news-item rounded-2xl border border-gray-200 overflow-hidden cursor-pointer shadow-sm hover:shadow-lg transition bg-white"
                @click="openModal(item)"
              >
                <div class="w-full h-[220px] overflow-hidden">
                  <img
                    :src="item.photo"
                    alt="Image"
                    class="w-full h-full object-cover"
                  />
                </div>

                <div class="p-4">
                  <div
                    class="flex items-center gap-2 text-sm text-gray-500 mb-2"
                  >
                    <span
                      class="inline-block px-2 py-1 rounded-full bg-blue-50 text-blue-600 font-medium"
                    >
                      {{ item.category }}
                    </span>
                    <span>•</span>
                    <span>{{ item.date }}</span>
                  </div>

                  <h3
                    class="text-lg font-bold text-gray-900 leading-snug mb-2 line-clamp-2"
                  >
                    {{ item.title }}
                  </h3>

                  <p class="text-sm text-gray-600 leading-6 mb-4 line-clamp-3">
                    {{ item.detail }}
                  </p>

                  <div class="flex items-center justify-between">
                    <div class="text-sm text-gray-500">โดย {{ item.name }}</div>

                    <span class="text-blue-600 font-semibold text-sm">
                      อ่านเพิ่มเติม →
                    </span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="dots">
            <span
              v-for="(dot, index) in totalPages"
              :key="index"
              class="dot"
              :class="{ active: index === currentIndex }"
              @click="goTo(index)"
            ></span>
          </div>
        </div>
      </div>

      <transition
        enter-active-class="transition duration-300 ease-out"
        enter-from-class="opacity-0"
        enter-to-class="opacity-100"
        leave-active-class="transition duration-200 ease-in"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <div
          v-if="showModal"
          class="fixed inset-0 z-[9999] bg-black/50 backdrop-blur-sm flex items-center justify-center px-4"
          @click.self="closeModal"
        >
          <div
            class="w-full max-w-2xl bg-white rounded-[28px] shadow-2xl overflow-hidden relative"
          >
            <div class="p-6 md:p-8">
              <button
                @click="closeModal"
                class="absolute top-4 right-4 w-10 h-10 rounded-full bg-slate-100 hover:bg-slate-200 text-slate-600 text-lg transition border-0"
              >
                ✕
              </button>

              <div v-if="selectedItem" class="pr-10">
                <div class="d-flex align-items-center mb-4">
                  <img
                    :src="selectedItem.photo"
                    alt="Image"
                    class="rounded-circle border"
                    style="width: 64px; height: 64px; object-fit: cover"
                  />
                  <div class="ml-3">
                    <p class="mb-1 text-primary font-weight-bold">
                      {{ selectedItem.date }}
                    </p>
                    <h3 class="mb-1 font-weight-bold">
                      {{ selectedItem.title }}
                    </h3>
                    <p class="mb-0 text-muted">
                      {{ selectedItem.category }} | {{ selectedItem.name }}
                    </p>
                  </div>
                </div>

                <div class="mt-4">
                  <p
                    class="text-muted"
                    style="line-height: 1.9; white-space: pre-line"
                  >
                    {{ selectedItem.detail }}
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<style scoped>
.news-slider {
  display: flex;
  gap: 20px;
  overflow-x: auto;
  scroll-behavior: smooth;
  scrollbar-width: none;
  -ms-overflow-style: none;
  padding-bottom: 8px;
}

.news-slider::-webkit-scrollbar {
  display: none;
}

.news-slide {
  flex: 0 0 calc((100% - 40px) / 3);
}

.news-item {
  height: 100%;
}

.custom-prev,
.custom-next {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 42px;
  height: 42px;
  border-radius: 999px;
  border: 1px solid #d1d5db;
  background: #fff;
  color: #111827;
  transition: all 0.2s ease;
  text-decoration: none;
}

.custom-prev:hover,
.custom-next:hover {
  background: #f3f4f6;
  color: #111827;
}

@media (max-width: 991px) {
  .news-slide {
    flex: 0 0 calc((100% - 20px) / 2);
  }
}

@media (max-width: 767px) {
  .news-slide {
    flex: 0 0 100%;
  }
}

.dots {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  gap: 8px;
}

.dot {
  width: 10px;
  height: 10px;
  background: #d1d5db;
  border-radius: 50%;
  cursor: pointer;
  transition: 0.3s;
}

.dot.active {
  background: #2563eb;
  width: 22px;
  border-radius: 999px;
}

.custom-prev.disabled,
.custom-next.disabled {
  opacity: 0.4;
  pointer-events: none;
  cursor: not-allowed;
}
</style>
