<template>
	<q-card>
		<q-card-section>
			<div class="text h6">Server name: {{ server.server_name }}
				<q-btn
					class="float-right"
					:color="(timepast > 30) ? 'red' : (timepast > 10) ? 'warning' : 'green'"
					icon="power_settings_new"
					size="sm"
					round
				></q-btn>
			</div>
			<div>Uptime: {{ server.data.cpu.uptime }}</div>
			<div>{{ server.tarih }}</div>
		</q-card-section>
		<q-separator />
		<q-card-section horizontal>
			<q-card-section class="col-4">
				<div class="text-h6">Load avarage</div>
				<q-list
					dense
					bordered
					separator
				>
					<q-item
						v-for="(l,x) in server.data.cpu.load"
						:key="'load_' + x"
					>
						<q-item-section v-if="x == 0">1 min. avg.</q-item-section>
						<q-item-section v-if="x == 1">5 min. avg.</q-item-section>
						<q-item-section v-if="x == 2">15 min. avg.</q-item-section>
						<q-item-section>{{ l }}</q-item-section>
					</q-item>
				</q-list>
			</q-card-section>
			<q-separator vertical />
			<q-card-section class="col-4">
				<div class="text-h6">Disk usage</div>

				<template
					v-for="(p,x) in disks"
					:key="'disk_' + x"
				>
					<q-linear-progress
						rounded
						size="25px"
						:value="p.percent / 100"
						:color="p.percent > 90 ? 'red' : (p.percent > 80 ? 'warning' : 'green')"
						class="q-mt-sm"
					>
						<div class="absolute-full flex flex-center">
							<q-badge
								color="white"
								text-color="accent"
								:label="'Disk ' + p.path + ' : ' + p.percent + '%'"
							/>
						</div>
					</q-linear-progress>

				</template>
				<div class="text-h6">Memory</div>
				<q-linear-progress
					rounded
					size="25px"
					:value="memory.percent / 100"
					:color="memory.percent > 90 ? 'red' : (memory.percent > 80 ? 'warning' : 'green')"
					class="q-mt-sm"
				>
					<div class="absolute-full flex flex-center">
						<q-badge
							color="white"
							text-color="accent"
							:label="memory.percent + '%'"
						/>
					</div>
				</q-linear-progress>
			</q-card-section>
			<q-separator vertical />
			<q-card-section class="col-4">
				<div class="text-h6">Services <small class="text-caption">Process count</small></div>
				<q-list>
					<q-list
						v-for="(s,x) in server.data.services"
						:key="'service_' + x"
					>
						<span :class="parseInt(s) > 0 ? 'text-green' : 'text-red'">{{ x }} : {{ s }}</span>
					</q-list>
				</q-list>
			</q-card-section>
		</q-card-section>
	</q-card>
</template>

<script>
import { defineComponent, computed, ref } from 'vue';

export default defineComponent({
	name: 'ServerStatus',
	props: {
		server: { type: Object, default: () => {} }
	},
	setup(props, ctx) {
		const timepast = ref(0);
		const disks = computed(() => {
			return Object.keys(props.server.data.disk).map((key) => {
				var d = props.server.data.disk[key];
				return {
					path: key,
					free: d.free,
					total: d.total,
					percent: parseFloat((((d.total - d.free) / d.total) * 100).toFixed(2))
				};
			});
		});
		const memory = computed(() => {
			var m = props.server.data.memory;
			return {
				free: m.free,
				total: m.total,
				used: m.used,
				percent: parseFloat((((m.total - m.free) / m.total) * 100).toFixed(2))
			};
		});
		var t = setInterval(() => {
			timepast.value = (new Date() - props.server.updated) / 1000;
		}, 10000);
		return {
			disks,
			memory,
			timepast
		};
	}
});
</script>
