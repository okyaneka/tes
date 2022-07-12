<script setup>
import { onMounted, ref } from "vue";

const page = ref("");
const min_price = ref("");
const max_price = ref("");
const data = ref([]);

async function getData() {
  const url = new URL("https://qnq.co.id/wp-json/wc/store/products");
  url.searchParams.append("page", page.value || 1);
  url.searchParams.append("per_page", 100);
  url.searchParams.append("orderby", "price");
  url.searchParams.append("order", "asc");
  url.searchParams.append("min_price", min_price.value || "");
  url.searchParams.append("max_price", max_price.value || "");
  data.value = await (await fetch(url)).json();
}

onMounted(async () => {
  getData();
});
</script>

<template>
  <div class="p-4 grid gap-4 w-full overflow-auto">
    <!-- form filter -->
    <form @submit.prevent="getData()">
      <div class="grid gap-4">
        <div class="grid grid-cols-4 gap-4">
          <input
            type="text"
            class="rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-green-500 focus:border-green-500 focus:z-10 sm:text-sm"
          />
          <input
            v-model="page"
            type="text"
            placeholder="Page"
            class="rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-green-500 focus:border-green-500 focus:z-10 sm:text-sm"
          />
          <input
            v-model="min_price"
            type="text"
            placeholder="Min Price"
            class="rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-green-500 focus:border-green-500 focus:z-10 sm:text-sm"
          />
          <input
            v-model="max_price"
            type="text"
            placeholder="Max Price"
            class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-green-500 focus:border-green-500 focus:z-10 sm:text-sm"
          />
        </div>
        <div>
          <button
            type="submit"
            href="#"
            class="rounded-md bg-green-600 px-6 py-2 text-white hover:bg-green-700"
          >
            Perbarui
          </button>
        </div>
      </div>
    </form>

    <div class="grid grid-cols-4 gap-4">
      <div
        v-for="(item, index) in data"
        :key="index"
        class="border rounded-lg p-4 bg-gray-100 hover:bg-gray-200 grid gap-4"
      >
        <div>
          <img
            :src="item?.images?.at(0)?.src"
            class="w-full max-h-64 object-contain"
          />
        </div>

        <div class="grid gap-1">
          <div class="line-through text-sm text-gray-500">
            {{ item.prices.currency_symbol }}
            {{
              parseInt(item.prices.regular_price.slice(0, -2)).toLocaleString(
                "id-ID"
              )
            }}
          </div>
          <div class="text-lg font-semibold text-green-700">
            {{ item.prices.currency_symbol }}
            {{
              parseInt(item.prices.sale_price.slice(0, -2)).toLocaleString("id-ID")
            }}
          </div>
        </div>

        <div class="font-semibold text-xl" v-html="item.name" />

        <div class="flex flex-row flex-wrap -m-1">
          <div v-for="(tag, i) in item.tags" :key="`tag-${i}`" class="m-1">
            <div
              class="px-2 py-1 text-xs rounded-full bg-green-100 hover:bg-green-200 shadow cursor-pointer"
            >
              {{ tag.name }}
            </div>
          </div>
        </div>

        <div class="grid gap-1">
          <div class="font-semibold text-xs">Categories:</div>
          <div class="flex flex-row flex-wrap -m-1">
            <div
              v-for="(cat, i) in item.categories"
              :key="`tag-${i}`"
              class="m-1"
            >
              <div
                class="px-2 py-1 text-xs rounded-full bg-yellow-100 hover:bg-yellow-200 shadow cursor-pointer"
              >
                {{ cat.name }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
