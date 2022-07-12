<script setup>
import { computed, onMounted, ref } from "vue";
import KButton from "../components/KButton.vue";

const loading = ref(false);
const data = ref();
const page = ref(1);
const limit = ref(10);

const pages = computed(() => {
  if (isNaN(page.value)) {
    return [];
  }
  return paginate({
    current: page.value,
    total: data.value?.data?.pagination?.total_page || 1,
    visibled: 5,
  });
});

function paginate({ current, total, visibled }) {
  let pageRange = [];
  if (total > visibled) {
    pageRange = new Array(visibled);
    pageRange = pageRange.fill(0).map((val, i) => {
      const mid = Math.floor(visibled / 2);
      const range = visibled - 2;
      const last = visibled - 1;

      if (current > range && current < total - mid) {
        switch (i) {
          case 0:
            return current > range ? "more" : i + current - mid;
          case last:
            return current < total - mid ? "less" : i + current - mid;
          default:
            return i + current - mid;
        }
      }

      if (current <= range) {
        return i == last ? "more" : i + 1;
      }

      if (current >= total - mid) {
        return i == 0 ? "less" : i + total - last;
      }
    });
  } else {
    pageRange = new Array(total);
    pageRange = pageRange.fill(0).map((val, i) => i + 1);
  }

  return pageRange;
}

function formatDate(date) {
  try {
    return new Date(date).toLocaleString();
  } catch (error) {
    return "-";
  }
}

async function loadData(inputPage = 1) {
  loading.value = true;

  if (inputPage == "more") page.value = pages.value[3] + 1;
  else if (inputPage == "less") page.value = pages.value[1] - 1;
  else page.value = inputPage;
  try {
    const url = new URL(
      "https://api-v1.momofin.com/v1/auth/dashboard/master-company"
    );
    url.searchParams.append("page", page.value || 1);
    url.searchParams.append("limit", limit.value || 1);
    data.value = await (await fetch(url)).json();
  } catch (error) {
    console.error(error);
  }
  loading.value = false;
}

onMounted(() => {
  loadData();
});
</script>

<template>
  <div
    v-if="loading"
    class="min-h-screen flex gap-4 items-center justify-center"
  >
    <span
      v-for="i in 4"
      :key="`dot-${i}`"
      class="animate-ping inline-block w-2 h-2 rounded-full bg-green-400"
      :style="{ animationDelay: (i - 0) * 0.05 + 's' }"
    />
  </div>

  <div v-else class="p-8 flex flex-col">
    <div class="overflow-x-auto mb-4">
      <div class="py-2 inline-block min-w-full">
        <div class="overflow-hidden">
          <table class="min-w-full">
            <thead class="border-b">
              <tr>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  No
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Tanggal Register
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Nama Lengkap
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Nama Perusahaan
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Email
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  No HP
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Alamat Perusahaan
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Status KYC Perusahaan
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Subdomain
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Custom Domain
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  Logo Perusahaan
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  NIB
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  File NIB
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  NPWP Perusahaan
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  File NPWP
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  NIK Direktur
                </th>
                <th
                  scope="col"
                  class="text-sm font-medium text-gray-900 px-6 py-4 text-left"
                >
                  File KTP
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, i) in data?.data?.company || []"
                :key="`data-${i}`"
                class="bg-white border-b hover:bg-green-50"
              >
                <td
                  class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900"
                >
                  {{ i + 1 + (page - 1) * limit }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ formatDate(item.created_at) }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.fullname || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.company_name || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.email || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.phone || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.address || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.company_verified || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.domain || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.custom_domain || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  <a v-if="item.logo" :href="item.logo" target="_blank">
                    <img :src="item.logo" />
                  </a>
                  <span v-else>-</span>
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.nib || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  <a
                    v-if="item.nib_image"
                    :href="item.nib_image"
                    target="_blank"
                  >
                    <img :src="item.nib_image" />
                  </a>
                  <span v-else>-</span>
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.npwp || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  <a
                    v-if="item.npwp_image"
                    :href="item.npwp_image"
                    target="_blank"
                  >
                    <img :src="item.npwp_image" />
                  </a>
                  <span v-else>-</span>
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  {{ item.ktp || "-" }}
                </td>
                <td
                  class="text-sm text-gray-900 font-light px-6 py-4 whitespace-nowrap"
                >
                  <a
                    v-if="item.ktp_image"
                    :href="item.ktp_image"
                    target="_blank"
                  >
                    <img :src="item.ktp_image" />
                  </a>
                  <span v-else>-</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <div class="flex gap-4">
      <k-button
        v-for="(thePage, i) in pages"
        :outlined="page == thePage"
        :key="`page-${i}`"
        :text="thePage"
        @click="loadData(thePage)"
      />
    </div>
  </div>
</template>
