<script setup>
	import FooTable from './components/FooTable.vue'
	import teamData from '../../Content/team.json'

	import { ref, onMounted } from 'vue';

	const loading = ref(true);
	const reload = ref(false);

	const simpleData = ref(teamData.table);

	onMounted(() => {
		setTimeout(() => {
			loading.value = false;
		}, 2000);
		freshTable()
	})

	const freshTable = () => {
		reload.value = false;
		simpleData.value.body.data = simpleData.value.body.data.map((item) => transformer(item))
		reload.value = true
	}

	const transformer = (data) => {
		return {
			team: data.team,
			day: data.day,
			time: getTimeForm(data.time),
			hall: data.hall
		}
	}

	const getTimeForm = (timeList) => {
		return `<div class='flex justify-center'><div class='bg-gray-500 text-xs text-white rounded-xl px-2 py-1 mr-2'>${timeList.start}</div> - <div class='bg-gray-500 text-xs text-white rounded-xl px-2 py-1 ml-2'>${timeList.end}</div></div>`;
	}
</script>

<template>
	<div class="bg-white p-5 container mx-auto">
		<FooTable
			:groupTable="true"
			:json="simpleData"
			groupKey="team"
			:loading="loading"
			v-if="reload"
		></FooTable>
	</div>
</template>

<style scoped>

</style>
