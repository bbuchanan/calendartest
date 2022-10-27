<script lang="ts">
	import { browser } from '$app/environment';
	import { onMount } from 'svelte';
	import dayjs from 'dayjs';

	let scheduler: any;

	let employees = [
		{ employeeId: 1, text: 'Bill Buchanan', color: '#727bd2' },
		{ employeeId: 2, text: 'Michael Vick', color: '#d53032' },
		{ employeeId: 3, text: 'Bill Young', color: '#2a7ee4' }
	];

	let data = [
		{
			id: 1,
			employeeId: 2,
			text: 'Sparring session for Rex',
			startDate: dayjs().add(-60, 'minute'),
			endDate: dayjs().add(-30, 'minute')
		},
		{
			id: 2,
			employeeId: 1,
			text: 'Full cut and wash for Daisy',
			startDate: dayjs(),
			endDate: dayjs().add(30, 'minute')
		}
	];

	let filteredData = [...data];

	const newAppt = (e) => {
		e.cancel = true;
		if (e.appointmentData.id) {
			console.log(e.appointmentData);
		} else {
			addAppoint(e.appointmentData.startDate, e.appointmentData.endDate, 'Nail clipping for Fionn');
		}
	};

	onMount(() => {
		if (browser) {
			jQuery('#scheduler').dxScheduler({
				resources: [
					{
						fieldExpr: 'employeeId',
						allowMultiple: false,
						dataSource: employees,
						label: 'Groomer',
						valueExpr: 'employeeId'
					}
				],
				dataSource: filteredData,
				appointmentTemplate(model) {
					const text = displayAppointment(model);
					return text;
				},
				onAppointmentFormOpening: function (e) {
					newAppt(e);
				},
				startDayHour: 9,
				endDayHour: 21,
				views: ['day', 'week', 'month'],
				currentView: 'day'
			});

			scheduler = jQuery('#scheduler').dxScheduler('instance');
		}
	});

	const displayAppointment = (model) => {
		const data = model.appointmentData;
		const emp = employees.find((x) => x.employeeId === data.employeeId);
		return `${data.text} <i>(${emp?.text})</i>`;
	};

	const addAppoint = (start: Date | null = null, end: Date | null = null, title: string = '') => {
		console.log(start);
		if (!start) {
			start = dayjs().toDate();
		}

		if (!end) {
			end = dayjs().add(30, 'minute').toDate();
		}

		const appt = {
			id: data.length + 1,
			text: title,
			employeeId: 3,
			startDate: start,
			endDate: end
		};
		data = [...data, appt];
		filteredData = [...data];
		scheduler.option('dataSource', filteredData);
	};

	let selectedEmployee: string;

	const onEmployeeSelectChanged = (evt) => {
		const selId = parseInt(selectedEmployee);
		if (selId !== -1) {
			filteredData = data.filter((x) => x.employeeId === selId);
		} else {
			filteredData = [...data];
		}

		console.log(filteredData);
		console.log(data);
		scheduler.option('dataSource', filteredData);
	};
</script>

<!-- <button on:click={() => addAppoint(null, null, 'Oh hai guise!')}>Add</button> -->
<select bind:value={selectedEmployee} on:change={onEmployeeSelectChanged}>
	<option value="-1">All</option>
	{#each employees as emp}
		<option value={emp.employeeId}>{emp.text}</option>
	{/each}
</select>
<div bind:this={scheduler} id="scheduler" />
