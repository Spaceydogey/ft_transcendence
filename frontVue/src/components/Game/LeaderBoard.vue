<script setup lang="ts">
import { useUserStore } from '@/stores/user';
import LeaderBoardUsr from './LeaderBoardUsr.vue';
import {type LeaderBoardUserInfo} from '@/types';
import axios from 'axios';
import {ref, onUnmounted} from 'vue';

const store = useUserStore();
const users = ref();
const LeaderBoardExists = ref(false);
async function getLeaderBoard() {
    try {
        const res = await axios.get(`http://${import.meta.env.VITE_LOCAL_IP}:${import.meta.env.VITE_BACKEND_PORT}/user/getLeaderBoard`);
        users.value = res.data.slice().sort((a:LeaderBoardUserInfo, b:LeaderBoardUserInfo) => b.elo*1 - a.elo*1);
		for (let user of users.value) {
			user.photo = await store.getAvatar(user.photo);
		}
        LeaderBoardExists.value = true;
    }
    catch(error) {

    }
}

await getLeaderBoard();

onUnmounted(async () => {
	for (let user of users.value)
		URL.revokeObjectURL(user.photo);
});
</script>

<template>
<h2 style="text-align: center; " class="m-5"> LeaderBoard </h2>
<div  v-if="LeaderBoardExists" class="card text-white bg-dark overflow-auto shadow-lg" style="max-width: 70%; max-height: 70vh; margin:auto;">
        <template v-for="(user, index) in users" :key="index">
            <LeaderBoardUsr :user="user" :index="index"> </LeaderBoardUsr>
        </template>
</div>
</template>