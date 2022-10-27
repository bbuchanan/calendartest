<script lang="ts">
	import { browser } from '$app/environment';
	import { onMount } from 'svelte';
	import dayjs from 'dayjs';

	let scheduler: any;

	let data = [
		{
			id: 1,
			text: 'uh oh',
			startDate: dayjs().add(-60, 'minute'),
			endDate: dayjs().add(-30, 'minute')
		}
	];

	const newAppt = (e) => {
		e.cancel = true;
		if (e.appointmentData.id) {
			console.log(e.appointmentData);
		} else {
			addAppoint(
				e.appointmentData.startDate,
				e.appointmentData.endDate,
				'Woah dude, nice appointment'
			);
		}
	};

	onMount(() => {
		if (browser) {
			jQuery('#scheduler').dxScheduler({
				adaptiveMode: true,
				dataSource: data,
				onAppointmentFormOpening: function (e) {
					newAppt(e);
				},
				views: [
					{
						type: 'day',
						startDayHour: 9,
						endDayHour: 21
					},
					{
						type: 'week',
						startDayHour: 10,
						endDayHour: 22
					},
					'month'
				],
				currentView: 'day'
			});

			scheduler = jQuery('#scheduler').dxScheduler('instance');
		}
	});

	const addAppoint = (start: Date | null = null, end: Date | null = null, title: string = '') => {
		console.log('Adding appointment');
		console.log(start);
		if (!start) {
			start = dayjs().toDate();
		}

		if (!end) {
			end = dayjs().add(30, 'minute').toDate();
		}

		console.log(start);
		scheduler.addAppointment({
			id: 2,
			text: title,
			startDate: start,
			endDate: end
		});
	};
</script>

<button on:click={() => addAppoint(null, null, 'Oh hai guise!')}>Add</button>
<div bind:this={scheduler} id="scheduler" />
