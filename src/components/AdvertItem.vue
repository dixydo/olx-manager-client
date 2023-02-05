<template>
  <th class="flex gap-3 px-6 py-4 font-normal text-gray-900">
    <!-- <div class="relative h-16 w-16">
            <img
              class="h-full w-full rounded-full object-cover object-center"
              src="https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80"
              alt=""
            />
            <span class="absolute right-0 bottom-0 h-2 w-2 rounded-full bg-green-400 ring ring-white"></span>
          </div> -->
    <div class="text-lg">
      <div class="font-medium text-gray-700">{{ post.Title }}</div>
      <!-- <div class="text-gray-400">jobs@sailboatui.com</div> -->
    </div>
  </th>
  <!-- <td class="px-6 py-4">
          <span
            class="inline-flex items-center gap-1 rounded-full bg-green-50 px-2 py-1 text-xs font-semibold text-green-600"
          >
            <span class="h-1.5 w-1.5 rounded-full bg-green-600"></span>
            Active
          </span>
        </td> -->
  <td class="px-6 py-4">{{ post.Price }}</td>
  <td class="px-6 py-4">
    <div class="flex gap-2">
      <span
        v-if="post.New"
        class="inline-flex items-center gap-1 rounded-full bg-blue-50 px-2 py-1 text-xs font-semibold text-blue-600"
      >
        New
      </span>
      <span
        v-if="!post.New"
        class="inline-flex items-center gap-1 rounded-full bg-gray-300 px-2 py-1 text-xs font-semibold text-gray-600"
      >
        Used
      </span>
    </div>
  </td>
  <td class="px-6 py-4">
    <div class="flex gap-2">
      <span
        v-if="post.Top"
        class="inline-flex items-center gap-1 rounded-full bg-blue-50 px-2 py-1 text-xs font-semibold text-blue-600"
      >
        Yes
      </span>
      <span
        v-if="!post.Top"
        class="inline-flex items-center gap-1 rounded-full bg-gray-300 px-2 py-1 text-xs font-semibold text-gray-600"
      >
        No
      </span>
    </div>
  </td>
  
  <teleport to="body">
    <EditPost v-if="openModal" :toggleModal="toggleModal" :post="post" />
  </teleport>
</template>

<script setup lang="ts">
import { ref, toRefs } from "vue";
import type { IPostResponse } from "@/api/types";
import EditPost from "@/components/EditPost.vue";
import { useMutation, useQueryClient } from "vue-query";
import { deletePostFn } from "@/api/postApi";
import { createToast } from "mosha-vue-toastify";

const VUE_APP_API_URL = "http://localhost:8080";
const props = defineProps<{
  post: IPostResponse;
}>();

const { post } = toRefs(props);

const queryClient = useQueryClient();
const { isLoading, mutate: deletePost } = useMutation((id: string) => deletePostFn(id), {
  onSuccess: (data) => {
    createToast("Post deleted successfully", {
      position: "top-right",
    });
    queryClient.invalidateQueries("posts");
  },
  onError: (error: any) => {
    if (Array.isArray((error as any).response.data.error)) {
      (error as any).response.data.error.forEach((el: any) =>
        createToast(el.message, {
          position: "top-right",
          type: "warning",
        })
      );
    } else {
      createToast((error as any).response.data.message, {
        position: "top-right",
        type: "danger",
      });
    }
  },
});

function onDelete() {
  if (window.confirm("Are you sure you want to delete this post?")) {
    deletePost(post.value.id);
  }
}

const openSettings = ref(false);
const openModal = ref(false);

function showSettings() {
  openSettings.value = true;
}

function toggleModal() {
  openModal.value = !openModal.value;
  openSettings.value = false;
}
</script>
