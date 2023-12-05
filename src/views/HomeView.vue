<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import ActivityItem from '../components/ActivityItem.vue';
import ModalDelete from '../components/ModalDelete.vue';
import ModalInfo from '../components/ModalInfo.vue';
// use email
const email = 'bqjzv@example.com'

const router = useRouter()
const showDelete = ref(false);
const showInfo = ref(false);
const activities = ref([]);
const toDelete = ref(null);
const handleDelete = (act) => {
  toDelete.value = act;
  showDelete.value = true;
};
const goTo = (id) => {
  router.push('/details/' + id)
}

function triggerInfo() {
  showInfo.value = true
  setTimeout(() => {
    showInfo.value = false
  }, 3000)
}

const getData = async () => {
  var requestOptions = {
    method: 'GET',
    redirect: 'follow',
  };
  await fetch('https://todo.api.devcode.gethired.id/activity-groups?email=' + email, requestOptions)
    .then(response => response.json())
    .then(result => {
      if (result.data) activities.value = result.data;
    })
    .catch(error => console.log('error', error));
  console.log(activities.value)
}

const addActivity = async () => {
  var body = JSON.stringify({
    "title": "New Activity",
    "email": email
  })
  var requestOptions = {
    method: 'POST',
    body: body,
    redirect: 'follow',
    headers: {
      "Content-Type": "application/json",
    },
  };

  await fetch("https://todo.api.devcode.gethired.id/activity-groups", requestOptions)
    .then(response => response.text())
    .then(result => {
      if (result) {
        getData()
      }
    })
    .catch(error => {
      if (error) {
        console.log('error', error)
      }
    });
}

const deleteActivity = async () => {
  var requestOptions = {
    method: 'DELETE',
    redirect: 'follow',
  };
  await fetch("https://todo.api.devcode.gethired.id/activity-groups/" + toDelete.value.id, requestOptions)
    .then(response => response.text())
    .then(result => console.log('deleted', result))
    .catch(error => console.log('error', error));

  getData()
  triggerInfo()
  showDelete.value = false
  toDelete.value = null
}

onMounted(() => {
  getData()
})
</script>

<template>
  <div class="container min-h-screen">
    <div class="flex justify-between mt-8">
      <h3 class="text-3xl text-slate-800 font-bold" data-cy="activity-title">Activity</h3>
      <div @click="addActivity" class="flex px-5 py-2 font-semibold bg-blue-500 text-lg text-white rounded-full"
        data-cy="activity-add-button">
        <span class="my-auto">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
            class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v12m6-6H6" />
          </svg>
        </span>
        <span class="px-2">Tambah</span>
      </div>
    </div>

    <div v-if="activities.length > 0" class="mt-6 grid grid-cols-2 lg:grid-cols-4 gap-4" data-cy="activity-list">
      <!-- activity list -->
      <ActivityItem v-for="activity, index in activities" :key="activity.id" :activity="activity"
        @delete="handleDelete(activity)" @detail="goTo(activity.id)" :data-cy="'activity-item-' + index" />
    </div>

    <div v-show="activities.length == 0"
      class="mt-6 w-full h-60 bg-gray-200 border border-b-2 border-b-gray-50 rounded-xl shadow-inner min-h-32"
      data-cy="activity-empty-state">
      <div class="w-full p-8 text-center text-gray-500">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="w-10 h-10 mx-auto">
          <path stroke-linecap="round" stroke-linejoin="round"
            d="M18.364 18.364A9 9 0 005.636 5.636m12.728 12.728A9 9 0 015.636 5.636m12.728 12.728L5.636 5.636" />
        </svg>

        <h3 class="mt-4 text-xl font-semibold">Ups, Aktivitas Masih Kosong.</h3>
        <p class="mt-4 font-bold text-blue-500" data-cy="tambah">
          Tambah Aktivitas
        </p>
      </div>
    </div>
  </div>
  <ModalDelete v-if="showDelete" :activity="toDelete" @cancel="showDelete = false, toDelete = null"
    @confirm="deleteActivity" />
  <ModalInfo v-if="showInfo" />
</template>
