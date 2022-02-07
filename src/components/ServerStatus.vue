<template>
	<q-card class="full-width">
		<q-card-section>
			<div class="text h6">Server name: {{ server.server_name }}</div>
			<div>Uptime: {{ server.data.cpu.uptime }}</div>
		</q-card-section>
		<q-separator />
		<q-card-section horizontal>
			<q-card-section class="col-3">
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
			<q-card-section class="col-3">
				<div class="text-h6">Disk usage</div>
				<template
					v-for="(p,x) in disks"
					:key="'disk_' + x"
				>
					<q-circular-progress
						:max="100"
						:value="p.percent"
						show-value
						size="50px"
						:color="p.percent > 95 ? 'red' : (p.percent > 80 ? 'orange' : 'green')"
						class="q-ma-md"
					>
						<span class="text-center">
							{{ p.path }} <br>
							{{ p.percent + ' %' }}
						</span>
					</q-circular-progress>
				</template>
			</q-card-section>
			<q-separator vertical />
			<q-card-section class="col-3">
				<div class="text-h6">Memory</div>
				<q-circular-progress
					:max="100"
					:value="memory.percent"
					show-value
					size="50px"
					class="q-ma-md"
				>
					<span class="text-center">
						{{ memory.percent }} %
					</span>
				</q-circular-progress>
			</q-card-section>
			<q-separator vertical />
			<q-card-section class="col-3">
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
import { defineComponent, computed } from 'vue';

export default defineComponent({
	name: 'ServerStatus',
	props: {
		server: { type: Object, default: () => {} }
	},
	setup(props, ctx) {
		const disks = computed(() => {
			return Object.keys(props.server.data.disk).map((key) => {
				var d = props.server.data.disk[key];
				return {
					path: key,
					free: d.free,
					total: d.total,
					percent: (((d.total - d.free) / d.total) * 100).toFixed(2)
				};
			});
		});
		const memory = computed(() => {
			var m = props.server.data.memory;
			return {
				free: m.free,
				total: m.total,
				used: m.used,
				percent: (((m.total - m.free) / m.total) * 100).toFixed(2)
			};
		});
		return {
			disks,
			memory
		};
	}
});
</script>
