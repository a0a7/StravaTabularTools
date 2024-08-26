<script lang="ts">
	import { createTable, createRender, Render, Subscribe } from 'svelte-headless-table';
	import {
		addPagination,
		addSortBy,
		addTableFilter,
		addHiddenColumns,
		addSelectedRows // @ts-expect-error: this import does exist
	} from 'svelte-headless-table/plugins';
	import { readable, writable } from 'svelte/store';
	import * as Table from '$lib/components/ui/table'; // @ts-expect-error: this import does exist
	import ArrowUpDown from 'lucide-svelte/icons/arrow-up-down'; // @ts-expect-error: this import does exist
	import ChevronDown from 'lucide-svelte/icons/chevron-down'; // @ts-expect-error: this import does exist
	import CaretSort from 'svelte-radix/CaretSort.svelte';
	import { cn } from '$lib/utils.js';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import * as DropdownMenu from '$lib/components/ui/dropdown-menu';
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import ActivityDescriptionCell from './ActivityDescriptionCell.svelte';
	import DataTableCheckbox from './DataTableCheckbox.svelte';

	import type { StravaActivity } from '$lib/activities';
	import ActivityTimeCell from './ActivityTimeCell.svelte';
	import Separator from '../ui/separator/separator.svelte';
	import ActivityTypeSelect from '../sidebar/map-config-components/ActivityTypeSelect.svelte';
	import CommuteSelect from '../sidebar/map-config-components/CommuteSelect.svelte';
	import DateRangePicker from '../sidebar/map-config-components/DateRangePicker.svelte';

	export let activityData: StravaActivity[];

	let startDate: any, endDate: any;
	const table = createTable(readable(activityData), {
		sort: addSortBy({ disableMultiSort: true }),
		searchFilter: addTableFilter({
			// @ts-expect-error: It's chill
			fn: ({ filterValue, value }) => value.includes(filterValue)
		}),
		commuteFilter: addTableFilter({
			// @ts-expect-error: It's chill
			fn: ({ commuteFilterValue, value }) => { 
				if (commuteFilterValue = 'dontFilter') {
					return true;
				} else if (commuteFilterValue = 'onlyCommutes') {
					return value.commute === true;
				} else if (commuteFilterValue = 'excludeCommutes') {
					return value.commute === false;
				} else {
					return true;
				}
			}
		}),
		disciplineFilter: addTableFilter({
			// @ts-expect-error: It's chill
			fn: ({ activityTypesFilteredFor, value }) => {
				console.log('Filtering by discipline: ', activityTypesFilteredFor, value);
				if (activityTypesFilteredFor !== undefined) {
					return activityTypesFilteredFor.includes(value);
				}
				return true;
			}
		}),

		hide: addHiddenColumns(),
		select: addSelectedRows()
	});
	const columns = table.createColumns([
		table.column({
			accessor: (item) => item,
			header: 'Name',
			id: 'name',
			cell: ({ value: { name, sport_type, id, commute } }) =>
				createRender(ActivityDescriptionCell, { name, id, sport_type }),
			plugins: {
				sort: {
					getSortValue({ name }: { name: string }) {
						return name;
					}
				},
				searchFilter: {
					exclude: false,
					getFilterValue({ name }: { name: string }) {
						return name.toLowerCase();
					}
				},
				commuteFilter: {
					exclude: false,
					getFilterValue({ commute }: { commute: string }) {
						return commute;
					}
				},
				disciplineFilter: {
					exclude: false,
					getFilterValue({ sport_type }: { sport_type: string }) {
						console.log('Filtering by discipline: ', sport_type);
						return sport_type;
					}
				}

			}
		}),
		table.column({
			accessor: 'elapsed_time',
			header: 'Elapsed Time',
			cell: ({ value }) =>
				createRender(ActivityTimeCell, { value }),
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},

				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'moving_time',
			header: 'Moving Time',
			cell: ({ value }) =>
				createRender(ActivityTimeCell, { value }),
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'distance',
			header: 'Distance',
			cell: ({ value }) => {
				if (value === 0) {
					return '-';
				}
				return (
					(value / 1000).toFixed(
						Math.max(1, 2 - (value === 0 ? -1 : Math.floor(Math.log10(value))))
					) + ' km'
				);
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'total_elevation_gain',
			header: 'Elevation',
			cell: ({ value }) => {
				if (value === 0) {
					return '-';
				}
				return `${value.toFixed(0)} m`;
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'kilojoules',
			header: 'Power Output',
			cell: ({ value }) => {
				if (value === undefined || value === 0) {
					return '-';
				}
				return `${value.toFixed(0)} kJ`;
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'average_speed',
			header: 'Average Speed',
			cell: ({ value }) => {
				if (value === 0) {
					return '-';
				}
				return `${(value * 3.6).toFixed(1)} kph`;
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: (item) => item,
			header: 'Average Cadence',
			id: 'average_cadence',
			cell: ({ value: { average_cadence, sport_type } }) => {
				if (!average_cadence || average_cadence === undefined || average_cadence === 0) {
					return '-';
				}
				if (sport_type === 'Ride' || sport_type === 'VirtualRide' || sport_type === 'EBikeRide' || sport_type === 'Handcycle' || sport_type === 'Velomobile' || sport_type === 'EMountainBikeRide' || sport_type === 'GravelRide' || sport_type === 'MountainBikeRide' || sport_type === 'Wheelchair' ) {
					return `${average_cadence.toFixed(1)} rpm`;
				}
				return `${average_cadence.toFixed(1)} spm`;
			},
			plugins: {
				sort: {
					getSortValue({ average_cadence }: { average_cadence: number }) {
						if (!average_cadence || average_cadence === undefined || average_cadence === 0) {
							return 0;
						}
						return average_cadence;
					}
				},
				searchFilter: {
					exclude: true,
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
			}
		}),

		table.column({
			accessor: 'average_watts',
			header: 'Average Power',
			cell: ({ value }) => {
				if (value === undefined) {
					return '-';
				}
				return `${value.toFixed(1)} W`;
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'average_heartrate',
			header: 'Average Heartrate',
			cell: ({ value }) => {
				if (value === undefined) {
					return '-';
				}
				return `${value.toFixed(0)} bpm`;
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: 'start_date_local',
			header: 'Date',
			cell: ({ value }) => {
				return new Date(value).toLocaleDateString();
			},
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
				sort: {
					getSortValue(value: number) {
						if (!value || value === undefined || value === 0) {
							return 0;
						}
						return value;
					}
				}
			}
		}),
		table.column({
			accessor: ({ id }) => id,
			header: '',
			id: 'idString',
			plugins: {
				searchFilter: {
					exclude: true
				},
				commuteFilter: {
					exclude: true
				},
				disciplineFilter: {
					exclude: true
				},
			}
		})
	]);

	const { headerRows, pageRows, tableAttrs, tableBodyAttrs, pluginStates, flatColumns, rows } =
		table.createViewModel(columns);
	
	console.log('activityData:', activityData);
	console.log('Page Rows:', $pageRows);

	const ids = flatColumns.map((col) => col.id);

	// const { pageIndex, hasNextPage, hasPreviousPage } = pluginStates.page;
	const { filterValue } = pluginStates.searchFilter;
	const { commuteFilterValue } = pluginStates.commuteFilter;
	const { activityTypesFilteredFor } = pluginStates.disciplineFilter;

	$: console.log(commuteFilterValue);

	const { hiddenColumnIds } = pluginStates.hide;
	const { selectedDataIds } = pluginStates.select;
	const { sortKeys } = pluginStates.sort;

	const colsHiddenByDefault = [
		'elapsed_time',
		'idString'
	];

	const hidableCols = [
		'elapsed_time',
		'moving_time',
		'distance',
		'total_elevation_gain',
		'kilojoules',
		'average_speed',
		'average_cadence',
		'average_watts',
		'average_heartrate',
		'start_date_local',
	];

	function isMobile() {
        return typeof window !== 'undefined' && window.innerWidth <= 768; 
    }
	
    if (isMobile()) {
        colsHiddenByDefault.push(
			'elapsed_time',
			'total_elevation_gain',
            'kilojoules',
            'average_speed',
            'average_cadence',
            'average_watts',
            'average_heartrate',
        );
    }

	let hideForId = Object.fromEntries(ids.map((id) => [id, !colsHiddenByDefault.includes(id)]));
	$: $hiddenColumnIds = Object.entries(hideForId)
		.filter(([, hide]) => !hide)
		.map(([id]) => id);

</script>

<div class="max-w-[calc(100vw-3.1rem)] md:max-w-[calc(100vw-5.6rem)]">
	<Separator class="mt-3 w-full" />
	<ScrollArea class="rounded-md" orientation="horizontal">
		<div class="flex space-x-1 pt-4">
			<ActivityTypeSelect bind:value={$activityTypesFilteredFor}/>
			<CommuteSelect bind:value={$commuteFilterValue}/>
			<DateRangePicker activities={activityData} bind:startDate bind:endDate />
		</div>
	</ScrollArea>

	<div class="flex items-center py-4">
		<Input
			class="max-w-72 mr-4 bg-background"
			placeholder="Search activities..."
			type="text"
			bind:value={$filterValue}
		/>
		<DropdownMenu.Root>
			<DropdownMenu.Trigger asChild let:builder>
				<Button variant="outline" class="ml-auto" builders={[builder]}>
					Columns <ChevronDown class="ml-2 h-4 w-4" />
				</Button>
			</DropdownMenu.Trigger>
			<DropdownMenu.Content>
				{#each flatColumns as col}
					{#if hidableCols.includes(col.id)}
						<DropdownMenu.CheckboxItem bind:checked={hideForId[col.id]}>
							{col.header}
						</DropdownMenu.CheckboxItem>
					{/if}
				{/each}
			</DropdownMenu.Content>
		</DropdownMenu.Root>
	</div>
	<ScrollArea class="h-[85vh] md:h-[55vh] max-w-[calc(100vw-2rem)] md:max-w-[calc(100vw-5rem)] rounded-md border" orientation="both">
		<Table.Root {...$tableAttrs} class="bg-background">
			<Table.Header>
				{#each $headerRows as headerRow}
					<Subscribe rowAttrs={headerRow.attrs()}>
						<Table.Row>
							{#each headerRow.cells as cell (cell.id)}
								<Subscribe attrs={cell.attrs()} let:attrs props={cell.props()} let:props>
									<Table.Head {...attrs} class="pr-0 [&:has([role=checkbox])]:pl-3">
										<Button variant="ghost" on:click={props.sort.toggle} class="px-[0.75rem]">
											<Render of={cell.render()} />
											<CaretSort
												class={cn(
													$sortKeys[0]?.id === cell.id && 'text-foreground',
													'ml-2 h-4 w-4'
												)}
											/>
										</Button>
									</Table.Head>
								</Subscribe>
							{/each}
						</Table.Row>
					</Subscribe>
				{/each}
			</Table.Header>
			<Table.Body {...$tableBodyAttrs}>
				{#each $pageRows as row (row.id)}
					<Subscribe rowAttrs={row.attrs()} let:rowAttrs>
						<Table.Row {...rowAttrs} data-state={$selectedDataIds[row.id] && 'selected'} class="">
							{#each row.cells as cell (cell.id)}
								<Subscribe attrs={cell.attrs()} let:attrs>
									<Table.Cell
										{...attrs}
										class={cell.id === 'name' ? 'truncate max-w-40 min-w-2' : ''}
									>
										<Render of={cell.render()} />
									</Table.Cell>
								</Subscribe>
							{/each}
						</Table.Row>
					</Subscribe>
				{/each}
			</Table.Body>
		</Table.Root>
	</ScrollArea>
</div>
