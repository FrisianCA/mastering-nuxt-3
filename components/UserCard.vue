<template>
  <div v-if="user" class="rounded p-3 flex items-center space-x-3 bg-white">
    <img
      :src="profile"
      alt="profile image"
      class="rounded-full w-12 h-12 border-2 border-blue-400"
    />
    <div class="text-right">
      <p class="font-medium">{{ username }}</p>
      <button class="text-sm underline text-slate-500" @click="logout">
        Log Out
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
const user = useSupabaseUser();
const { auth } = useSupabaseClient();

const logout = async () => {
  const { error } = await auth.signOut();

  if (error) {
    console.error(error);
    return;
  }

  // Supabase auth should be doing this itself

  try {
    await $fetch("/api/_supabase/session", {
      method: "POST",
      body: { event: "SIGNED_OUT", session: null },
    });
    user.value = null;
  } catch (e) {
    console.log(e);
  }

  await navigateTo("/login");
};

const username = computed(() => user.value?.user_metadata.user_name);
const profile = computed(() => user.value?.user_metadata.avatar_url);
</script>
