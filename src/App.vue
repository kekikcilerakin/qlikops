<template>
	<div class="app">
		<!-- Sidebar -->
		<Sidebar />

		<!-- Content -->
		<router-view />
	</div>
</template>

<script setup>
import Sidebar from './components/Sidebar.vue'
</script>

<script>
import config from "./qConfig";

export default {
	async mounted() {
		const $this = this;
		await $this.loadCapabilityApis();

		window.require.config({
		baseUrl: `${
			(config.isSecure ? "https://" : "http://") +
			config.host +
			(config.port ? `:${config.port}` : "") +
			"/" +
			config.prefix
		}/resources`,
		paths: {
			qlik: `${
			(config.isSecure ? "https://" : "http://") +
			config.host +
			(config.port ? `:${config.port}` : "") +
			"/" +
			config.prefix
			}/resources/js/qlik`,
		},
		});

		window.require(["js/qlik"], (qlik) => {
		window.qlik = qlik;
		console.log("global", qlik.getGlobal(config));
		qlik.getGlobal(config).getAppList(function (res) {
			console.log("res", res);
		});
		});
	},
	methods: {
		loadCapabilityApis() {
			const capabilityApisJS = document.createElement("script");
			capabilityApisJS.src = `${
				(config.isSecure ? "https://" : "http://") +
				config.host +
				(config.port ? `:${config.port}` : "") +
				"/" +
				config.prefix
			}/resources/assets/external/requirejs/require.js`;
			document.head.insertBefore(capabilityApisJS, document.head.firstElementChild);

			capabilityApisJS.loaded = new Promise((resolve) => {
				capabilityApisJS.onload = () => {
				resolve(true);
				};
			});

			const capabilityApisCSS = document.createElement("link");
			capabilityApisCSS.href = `${
				(config.isSecure ? "https://" : "http://") +
				config.host +
				(config.port ? `:${config.port}` : "") +
				"/" +
				config.prefix
			}/resources/autogenerated/qlik-styles.css`;
			capabilityApisCSS.type = "text/css";
			capabilityApisCSS.rel = "stylesheet";
			document.head.insertBefore(capabilityApisCSS, document.head.firstElementChild);
			capabilityApisCSS.loaded = new Promise((resolve) => {
				capabilityApisCSS.onload = () => {
				resolve(true);
				};
			});
			return Promise.all([capabilityApisJS.loaded, capabilityApisCSS.loaded]);
		},
	},
};
</script>

<style lang="scss">
:root {
	--primary: #4ade80;
	--grey: #64748b;
	--dark: #1e293b;
	--dark-alt: #334155;
	--light: #f1f5f9;
	--sidebar-width: 300px;
}

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Fira sans', sans-serif;
}

body {
	background: var(--light);
}

button {
	cursor: pointer;
	appearance: none;
	border: none;
	outline: none;
	background: none;
}

.app {
	display: flex;
	main {
		flex: 1 1 0;
		padding: 2rem;
		@media (max-width: 1024px) {
			padding-left: 6rem;
		}
	}
}

</style>
