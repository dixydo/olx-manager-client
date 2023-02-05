<template>
  <Header />
  <LoadingProgress v-if="isLoading" />

  <div v-else class="overflow-hidden rounded-lg border border-gray-200 shadow-md m-5">
    <table class="w-full border-collapse bg-white text-left text-sm text-gray-500">
      <thead class="bg-gray-50">
        <tr>
          <th scope="col" class="px-6 py-4 font-medium text-gray-900">Title</th>
          <th scope="col" class="px-6 py-4 font-medium text-gray-900">Price</th>
          <th scope="col" class="px-6 py-4 font-medium text-gray-900">Condition</th>
          <th scope="col" class="px-6 py-4 font-medium text-gray-900">Promoted</th>
          <th scope="col" class="px-6 py-4 font-medium text-gray-900"></th>
        </tr>
      </thead>
      <tbody class="divide-y divide-gray-100 border-t border-gray-100">
        <tr v-for="post in posts" class="hover:bg-gray-50">
          <AdvertItem :post="post" />
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { getAllPostsFn } from "@/api/postApi";
import Header from "@/components/Header.vue";
import LoadingProgress from "@/components/LoadingProgress.vue";
import { useQuery } from "vue-query";
import AdvertItem from "@/components/AdvertItem.vue";

const { data: posts, isLoading } = useQuery(["posts"], () => getAllPostsFn(), {
  select: (data) => data,
});
</script>
