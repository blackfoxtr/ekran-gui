<template>
	<q-page class="flex flex-center q-px-md">
		<template v-if="Object.values(serverList).length > 0">
			<server-status
				v-for="(s,x) in serverList"
				:key="'server_' + x"
				:server="s"
			/>
		</template>
		<template v-else>
			<q-banner class="bg-warning full-width">
				There is no server data yet.
			</q-banner>
		</template>
	</q-page>
</template>

<script>
import { defineComponent, onMounted, reactive } from 'vue';
import Pusher from 'pusher-js';
import ServerStatus from '../components/ServerStatus.vue';
import { useQuasar } from 'quasar';
export default defineComponent({
	name: 'PageIndex',
	components: {
		'server-status': ServerStatus
	},
	setup() {
		var pusher;
		var channel;
		const $q = useQuasar();
		const serverList = reactive({});
		onMounted(() => {
			var apikey = localStorage.getItem('pusher_apikey');
			var cluster = localStorage.getItem('pusher_cluster');
			if (!apikey || !cluster) {
				$q.notify({
					color: 'negative',
					message: 'You need to set API key and cluster to start monitoring.'
				});
			} else {
				pusher = new Pusher('407224d918b3ad00adf9', {
					cluster: 'eu'
				});
				channel = pusher.subscribe('server-status');
				channel.bind('update', function (data) {
					// var t = JSON.stringify(data);
					// console.log(data);
					// console.log({ [data.server_name]: data });
					Object.assign(serverList, { [data.server_name]: data });
					// serverList[data.server_name] = data;
				});
			}
		});
		return {
			serverList
		};
	}
});
</script>
