<template>
  <div>
    <Head>
      <Title>Список продуктів</Title>
    </Head>
    <UCard>
      <UTable
          :columns="columns"
          :rows="filteredProducts"
          :sort="true"
          :pagination="{ page, pageSize }"
          @update:page="page = $event"
      >
        <template #cell(rating)="{ row }">
          <span :class="row.rating < 4.5 ? 'text-red-500' : 'text-green-500'">
            {{ row.rating }}
          </span>
        </template>
        <template #cell(thumbnail)="{ row }">
          <img :src="row.thumbnail" alt="thumbnail" width="100" height="100" />
        </template>
      </UTable>

      <UInput
          v-model="search"
          icon="i-heroicons-magnifying-glass-20-solid"
          placeholder="Пошук..."
          class="mt-4"
      />
    </UCard>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useFetch } from '#app'

const { data } = await useFetch('https://dummyjson.com/products')
const products = ref(data.value.products)

const search = ref('')
const page = ref(1)
const pageSize = 5

const filteredProducts = computed(() => {
  return products.value.filter((product) =>
      product.title.toLowerCase().includes(search.value.toLowerCase())
  )
})

const columns = [
  { key: 'title', label: 'Назва' },
  { key: 'description', label: 'Опис' },
  { key: 'price', label: 'Ціна' },
  { key: 'rating', label: 'Оцінка' },
  { key: 'brand', label: 'Бренд' },
  { key: 'category', label: 'Категорія' },
  { key: 'thumbnail', label: 'Фото' },
]
</script>
