<template>
	<q-page class="flex flex-center q-pa-lg">
		<q-card class="full-width">
			<q-card-section>
				<div class="text-h6">Settings</div>
			</q-card-section>
			<q-card-section>
				<q-form class="q-gutter-md">
					<q-input
						v-model="apikey"
						label="API key"
					/>
					<q-select
						filled
						v-model="cluster"
						:options="clusterOptions"
						label="Cluster"
					/>
					<q-btn
						@click="save"
						color="primary"
						label="Save"
						icon="save"
					/>
				</q-form>
			</q-card-section>
		</q-card>
	</q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import { useQuasar } from 'quasar';

export default defineComponent({
	name: 'Settings',
	setup() {
		const $q = useQuasar();
		const apikey = ref('');
		const cluster = ref('eu');
		const clusterOptions = ['eu', 'us'];
		onMounted(() => {
			apikey.value = localStorage.getItem('pusher_apikey');
			cluster.value = localStorage.getItem('pusher_cluster');
		});
		const save = () => {
			localStorage.setItem('pusher_apikey', apikey.value);
			localStorage.setItem('pusher_cluster', cluster.value);
			$q.notify({
				color: 'positive',
				message: 'Saved'
			});
		};

		return {
			apikey,
			cluster,
			clusterOptions,
			save
		};
	}
});
</script>
