<script>
	import Breadcrumb from '../components/Breadcrumb/Breadcrumb.svelte';
	import CustomDropdown from '../components/CustomDropdown.svelte';
	import Table from '../components/Table/Table.svelte';
	import { createTableStore } from '../stores/stores/tableStore';
	import rosterData from '../utils/data/table/eventsTable.json';
	import { writable } from 'svelte/store';
	import { derived } from 'svelte/store';

	const breadcrumbsData = [
		{ name: 'Home', href: '/' },
		{ name: 'Events', href: '/' },
		{ name: 'Booking', href: '/' }
	];

	const tableHeaders = [
		{
			visible: true,
			key: 'RosterPerformer',
			title: 'Performer',
			width: '30%',
			sortable: false,
			align: 'left',
			customRender: (rosterPerformer) => {
				const profile = rosterPerformer.User.performerProfile;
				const avatar = profile.avatarPosition1 || '/default-avatar.jpg';
				const name = profile.stageName || `${profile.firstName} ${profile.lastName}`;

				return `<div class="flex items-center gap-3">
                <svg width="8" height="8" viewBox="0 0 8 8" fill="none" xmlns="http://www.w3.org/2000/svg" class="cursor-move">
                    <circle cx="2" cy="2" r="1" fill="#666C79"/>
                    <circle cx="6" cy="2" r="1" fill="#666C79"/>
                    <circle cx="2" cy="6" r="1" fill="#666C79"/>
                    <circle cx="6" cy="6" r="1" fill="#666C79"/>
                </svg>
                <img src="${avatar}" alt="${name}" class="w-10 h-10 rounded-full object-cover" />
                <span class="text-sm font-medium text-gray-900">${name}</span>
            </div>`;
			}
		},
		{
			visible: true,
			key: 'acceptedState',
			title: 'Status',
			width: '15%',
			sortable: true,
			align: 'center',
			customRender: (value) => {
				const status = {
					0: '<div class="flex justify-center" style="justify-content: center"><span class="w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-gray-100 text-gray-800"><img class="px-1" src="src/assets/svg/edit-user-02.png" alt="tick"> Invited</span></div>',
					1: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-yellow-100 text-yellow-800">Pending</span></div>',
					2: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-green-100 text-green-800"><img class="px-1" src="src/assets/svg/tick-circle.png" alt="tick"> Confirmed</span></div>',
					3: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-[#FF922E26] text-[#FF922E]"><img class="px-1" style="width: 20px" src="src/assets/svg/pin.svg" alt="tick"> Pinned</span></div>',
					4: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-[#FF666626] text-[#FF6666]"><img class="px-1" style="width: 20px" src="src/assets/svg/cancel.svg" alt="tick"> Declined</span></div>'
				};
				return status[value] || status[0];
			}
		},
		{
			visible: true,
			key: 'roleOnStage',
			title: 'Position',
			width: '15%',
			sortable: true,
			align: 'center',
			customRender: (value) => {
				const position = {
					0: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#ADB0B7]">HOST</span></div>',
					1: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#38A0FF]">FEATURE</span></div>',
					2: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#FF6666]">HEADLINER</span></div>',
					3: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#00AC87]">GUEST</span></div>'
				};
				return position[value] || '';
			}
		},
		{
			visible: true,
			key: 'setLength',
			title: 'Set',
			width: '10%',
			sortable: true,
			align: 'center',
			customRender: (value) => `<div class="text-center">${value}</div>` // Changed to center alignment
		},
		{
			visible: true,
			key: 'performerNotes',
			title: 'Notes',
			width: '30%',
			sortable: false,
			align: 'center',
			customRender: (value) => `<div class="text-center">${value}</div>` // Changed to center alignment
		}
	];

	const tableHeaders2 = [
		{
			visible: true,
			key: 'RosterPerformer',
			title: 'Performer',
			width: '30%',
			sortable: false,
			align: 'left',
			searchable: true, // Keeping tableHeaders2 specific property
			customRender: (rosterPerformer) => {
				const profile = rosterPerformer.User.performerProfile;
				const avatar = profile.avatarPosition1 || '/default-avatar.jpg';
				const name = profile.stageName || `${profile.firstName} ${profile.lastName}`;

				return `<div class="flex items-center gap-3">
                <svg width="8" height="8" viewBox="0 0 8 8" fill="none" xmlns="http://www.w3.org/2000/svg" class="cursor-move">
                    <circle cx="2" cy="2" r="1" fill="#666C79"/>
                    <circle cx="6" cy="2" r="1" fill="#666C79"/>
                    <circle cx="2" cy="6" r="1" fill="#666C79"/>
                    <circle cx="6" cy="6" r="1" fill="#666C79"/>
                </svg>
                <img src="${avatar}" alt="${name}" class="w-10 h-10 rounded-full object-cover" />
                <span class="text-sm font-medium text-gray-900">${name}</span>
            </div>`;
			}
		},
		{
			visible: true,
			key: 'acceptedState',
			title: 'Status',
			width: '15%',
			sortable: true,
			align: 'center',
			customRender: (value) => {
				const status = {
					0: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-gray-100 text-gray-800"><img class="px-1" src="src/assets/svg/edit-user-02.png" alt="tick"> Invited</span></div>',
					1: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-yellow-100 text-yellow-800">Pending</span></div>',
					2: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-green-100 text-green-800"><img class="px-1" src="src/assets/svg/tick-circle.png" alt="tick"> Confirmed</span></div>',
					3: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-[#FF922E26] text-[#FF922E]"><img class="px-1" style="width: 20px" src="src/assets/svg/pin.svg" alt="tick"> Pinned</span></div>',
					4: '<div class="flex justify-center"><span class="text-[#15A033] w-[100px] inline-flex items-center justify-center px-[18px] py-[4px] rounded-[6px] text-xs font-medium bg-[#FF666626] text-[#FF6666]"><img class="px-1" style="width: 20px" src="src/assets/svg/cancel.svg" alt="tick"> Declined</span></div>'
				};
				return status[value] || status[0];
			}
		},
		{
			visible: true,
			key: 'roleOnStage',
			title: 'Position',
			width: '15%',
			sortable: true,
			align: 'center',
			customRender: (value) => {
				const position = {
					0: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#ADB0B7]">HOST</span></div>',
					1: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#38A0FF]">FEATURE</span></div>',
					2: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#FF6666]">HEADLINER</span></div>',
					3: '<div class="flex justify-center"><span class="w-[100px] inline-flex items-center justify-center px-3 py-1 rounded-md text-sm font-medium text-[#fff] bg-[#00AC87]">GUEST</span></div>'
				};
				return position[value] || '';
			}
		},
		{
			visible: true,
			key: 'setLength',
			title: 'Set',
			width: '10%',
			sortable: true,
			align: 'center',
			customRender: (value) => `<div class="text-center">${value}</div>` // Changed to text-center
		},
		{
			visible: true,
			key: 'performerNotes',
			title: 'Notes',
			width: '30%',
			sortable: false,
			align: 'center',
			customRender: (value) => `<div class="text-center">${value}</div>` // Changed to text-center
		}
	];

	const tableStore = createTableStore(rosterData, 'id');
	let { sortedData } = tableStore;

	const allData = derived(sortedData, ($sortedData) =>
		$sortedData.filter((d) => d.acceptedState != 3 && d.acceptedState != 4)
	);

	const pinnedData = derived(sortedData, ($sortedData) =>
		$sortedData.filter((d) => d.acceptedState === 3)
	);
	const declinedData = derived(sortedData, ($sortedData) =>
		$sortedData.filter((d) => d.acceptedState === 4)
	);

	const searchQuery = writable('');
	const tableActions = {
		items: [
			{ label: 'Message Performer', action: () => console.log('Message') },
			{ label: 'Send reminder', action: () => console.log('Send reminder') },
			{ label: 'Display on event page', action: () => console.log('Display') }
		]
	};
	const tableActionsTwo = {
		items: [
			{ label: 'Message Performer', action: () => console.log('Message') },
			{ label: 'View invitation history', action: () => console.log('Send reminder') }
		]
	};
	const tableActionsThree = {
		items: [
			{ label: 'View profile', action: () => console.log('Send reminder') },
			{ label: 'Message Performer', action: () => console.log('Message') }
		]
	};

	const tableConfig = {
		searchable: false,
		hasCheckBox: false,
		bordered: false,
		isRounded: true,
		showTableHead: true,
		keyField: 'id',
		isDraggable: true,
		showRowNumbers: false,
		hasActions: true,
		actionsContent: tableActions,
		showFilters: false,
		styles: {
			container: 'w-full',
			table: 'min-w-full border-collapse overflow-hidden rounded-t-xl',
			thead: 'bg-[#F7F7F8] h-[60px]',
			tbody: 'bg-white divide-y divide-gray-200',
			tr: 'h-[60px]',
			th: 'px-6 text-center text-sm font-medium text-gray-600 tracking-wider', // Added text-center
			td: 'px-6 whitespace-nowrap text-sm text-gray-900 text-center' // Added text-center
		},
		allowHtml: true,
		paginated: {
			status: false,
			totalPages: Math.ceil(rosterData.length / 10),
			pageSize: 10
		}
	};

	const tableConfig2 = {
		searchable: true,
		showFilters: true,
		hasCheckBox: false,
		bordered: false,
		isRounded: true,
		showTableHead: true,
		keyField: 'id',
		isDraggable: true,
		showRowNumbers: false,
		hasActions: true,
		actionsContent: tableActionsTwo,
		styles: {
			container: 'w-full',
			table: 'min-w-full border-collapse overflow-hidden rounded-t-xl',
			thead: 'bg-[#F7F7F8] h-[60px]',
			tbody: 'bg-white divide-y divide-gray-200',
			tr: 'h-[60px]',
			th: 'px-6 text-center text-sm font-medium text-gray-600 tracking-wider',
			td: 'px-6 whitespace-nowrap text-sm text-gray-900 text-center'
		},
		allowHtml: true,
		paginated: {
			status: false,
			totalPages: Math.ceil(rosterData.length / 10),
			pageSize: 10
		}
	};

	const tableConfig3 = {
		searchable: true,
		showFilters: false,
		hasCheckBox: false,
		bordered: false,
		isRounded: true,
		showTableHead: true,
		keyField: 'id',
		isDraggable: true,
		showRowNumbers: false,
		hasActions: true,
		actionsContent: tableActionsThree,
		styles: {
			container: 'w-full',
			table: 'min-w-full border-collapse overflow-hidden rounded-t-xl',
			thead: 'bg-[#F7F7F8] h-[60px]',
			tbody: 'bg-white divide-y divide-gray-200',
			tr: 'h-[60px]',
			th: 'px-6 text-center text-sm font-medium text-gray-600 tracking-wider',
			td: 'px-6 whitespace-nowrap text-sm text-gray-900 text-center'
		},
		allowHtml: true,
		paginated: {
			status: false,
			totalPages: Math.ceil(rosterData.length / 10),
			pageSize: 10
		}
	};
</script>

<div class="flex w-full max-w-[1440px]">
	<div class="w-[22%]">
		<!-- Sidebar content -->
	</div>
	<div class="flex w-[78%] flex-col gap-8 bg-[#f7f7f8] p-4">
		<Breadcrumb data={breadcrumbsData} />
		<div class="flex flex-col gap-6 rounded-2xl bg-white px-6 py-3 pt-6">
			<p class="color-primary px-6 text-xl">Booking</p>
			<div>
				<div class="flex items-center justify-between px-6">
					<p class="color-tertiary flex items-center gap-2 text-2xl">
						<img class="h-6 w-6" src="src/assets/icons/user.png" alt="user avatar" />
						Lineup
					</p>
					<CustomDropdown placeholder={'Actions'} />
				</div>
				<div class="px-2">
					<Table columns={tableHeaders} data={$allData} {searchQuery} {...tableConfig} />
				</div>
			</div>

			<div>
				<div class="flex items-center justify-between px-6">
					<p class="color-tertiary flex items-center gap-2 text-2xl">
						<img class="h-6 w-6" src="src/assets/svg/pin.svg" alt="user avatar" />
						Pinned
					</p>
					<CustomDropdown placeholder={'Actions'} />
				</div>
				<div class="px-2">
					<Table columns={tableHeaders} data={$pinnedData} {searchQuery} {...tableConfig} />
				</div>
			</div>

			<div>
				<div class="flex items-center justify-between px-6">
					<p class="color-tertiary flex items-center gap-2 text-2xl">
						<img class="h-6 w-6" src="src/assets/icons/Subtract.png" alt="user avatar" />
						Declined
					</p>
				</div>
				<div class="px-2">
					<Table columns={tableHeaders} data={$declinedData} {searchQuery} {...tableConfig3} />
				</div>
			</div>

			<div>
				<div class="flex items-center justify-between px-6">
					<p class="color-tertiary flex items-center gap-2 text-2xl">
						<img class="h-4 w-5" src="src/assets/svg/eye-vector.png" alt="user avatar" />
						Roster
					</p>
				</div>
				<div class="px-2">
					<Table columns={tableHeaders2} data={$declinedData} {searchQuery} {...tableConfig2} />
				</div>
			</div>
		</div>
	</div>
</div>

<style>
	.color-primary {
		color: #0d1526;
	}

	.color-tertiary {
		color: #666c79;
	}
</style>
