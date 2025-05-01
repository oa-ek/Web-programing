<template>
  <div class="p-4">
    <Head>
      <Title>Список продуктів</Title>
    </Head>

    <h1 class="text-2xl font-bold mb-4">Список продуктів</h1>

    <UTable
        v-model:sort="sort"
        :rows="filteredRows"
        :columns="columns"
        :sort-mode="'manual'"
        :page-size="10"
        :ui="{ td: { base: 'align-middle' } }"
    >
      <template #thumbnail-data="{ row }">
        <img
            :src="row.thumbnail"
            alt="image"
            width="100"
            height="100"
            class="object-cover rounded"
        />
      </template>

      <template #rating-data="{ row }">
        <span
            :class="row.rating >= 4.5 ? 'text-green-600 font-bold' : 'text-red-600 font-bold'"
        >
          {{ row.rating }}
        </span>
      </template>
    </UTable>

    <UInput
        v-model="search"
        placeholder="Пошук..."
        icon="i-heroicons-magnifying-glass-20-solid"
        class="mt-4 w-1/2"
    />
  </div>
</template>

<script setup>
import { ref, computed, watchEffect } from 'vue'

const search = ref('')
const sort = ref({ column: '', direction: '' })

const { data } = await useFetch('https://dummyjson.com/products')

const rows = ref([])

watchEffect(() => {
  if (data.value?.products) {
    rows.value = data.value.products
  }
})

const filteredRows = computed(() => {
  let result = [...rows.value]

  if (search.value) {
    const term = search.value.toLowerCase()
    result = result.filter(row =>
        Object.values(row).some(val =>
            String(val).toLowerCase().includes(term)
        )
    )
  }

  if (sort.value.column) {
    result.sort((a, b) => {
      const dir = sort.value.direction === 'asc' ? 1 : -1
      const valA = a[sort.value.column]
      const valB = b[sort.value.column]
      return valA > valB ? dir : valA < valB ? -dir : 0
    })
  }

  return result
})

const columns = [
  { id: 'title', label: 'Назва', sortable: true },
  { id: 'description', label: 'Опис', sortable: true },
  { id: 'price', label: 'Ціна', sortable: true },
  { id: 'rating', label: 'Оцінка', sortable: true },
  { id: 'brand', label: 'Бренд', sortable: true },
  { id: 'category', label: 'Категорія', sortable: true },
  { id: 'thumbnail', label: 'Фото' }
]

</script>