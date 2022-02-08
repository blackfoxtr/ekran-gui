<template>
	<q-layout view="lHh Lpr lFf">
		<q-header elevated>
			<q-toolbar>
				<q-btn
					flat
					dense
					round
					icon="menu"
					aria-label="Menu"
					@click="toggleLeftDrawer"
				/>
				<q-toolbar-title> Ekran </q-toolbar-title>
				<div>
					v0.1
					<q-btn
						@click="quitApp"
						size="sm"
						color="info"
						icon="logout"
						title="Quit"
					>
					</q-btn>
				</div>
			</q-toolbar>
		</q-header>

		<q-drawer
			v-model="leftDrawerOpen"
			show-if-above
			bordered
		>
			<q-list>
				<EssentialLink
					v-for="link in essentialLinks"
					:key="link.title"
					v-bind="link"
				/>
			</q-list>
		</q-drawer>

		<q-page-container>
			<router-view />
		</q-page-container>
	</q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink.vue';

const linksList = [
	{
		title: 'Watch',
		caption: 'Server status screen',
		icon: 'personal_video',
		link: '/'
	},
	{
		title: 'Settings',
		caption: 'API settings',
		icon: 'settings',
		link: 'settings'
	}
];

import { defineComponent, ref } from 'vue';

export default defineComponent({
	name: 'MainLayout',

	components: {
		EssentialLink
	},

	setup() {
		const leftDrawerOpen = ref(false);
		const quitApp = () => {
			if (process.env.MODE === 'electron') {
				window.EkranWindowAPI.close();
			}
		};
		return {
			essentialLinks: linksList,
			leftDrawerOpen,
			toggleLeftDrawer() {
				leftDrawerOpen.value = !leftDrawerOpen.value;
			},
			quitApp
		};
	}
});
</script>
