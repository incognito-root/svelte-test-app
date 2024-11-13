<script lang="ts">
	import { cn } from './../../utils/utils';
	import { Checkbox, Dropdown, DropdownItem, Radio } from 'flowbite-svelte';
	import { DotsHorizontalOutline } from 'flowbite-svelte-icons';
	import { onMount } from 'svelte';
	// import SwipeListener from "swipe-listener";
	import type { TableColumn, TableProps } from './types';

	export let tdStyle;
	export let trStyle;
	export let classes: TableProps['classes'];
	export let keyField: TableProps['keyField'];
	export let bordered: TableProps['bordered'];
	export let hasCheckBox: TableProps['hasCheckBox'] = false;
	export let hasRadioButton: TableProps['hasRadioButton'] = false;
	export let hasActions: TableProps['hasActions'] = false;
	export let columns: TableColumn[];
	export let isDraggable: TableProps['isDraggable'];
	export let row;
	export let index;
	export let selectedRows;
	export let handleDragStart;
	export let handleDrop;
	export let toggleRowExpansion;
	export let toggleRowSelection;
	export let toggleRowSelectionRadio;
	export let mobileView;
	export let screenSize: TableProps['screenSize'];
	export let actionsContent; // Slot for custom actions

	function handleCheckboxChange(event) {
		event.stopPropagation(); // Stop the event from bubbling up to the row
	}

	function handleDropdownClick(event) {
		event.stopPropagation(); // Prevent the event from bubbling up to the row
	}

	function handleKeydown(event: KeyboardEvent) {
		const { key } = event;
		switch (key) {
			case 'ArrowRight':
				// Move to the next cell
				const nextCell = (event.target as HTMLElement).nextElementSibling;
				if (nextCell) (nextCell as HTMLElement)?.focus();
				break;
			case 'ArrowLeft':
				// Move to the previous cell
				const prevCell = (event.target as HTMLElement).previousElementSibling;
				if (prevCell) (prevCell as HTMLElement)?.focus();
				break;
			case 'ArrowDown':
				// Move to the next row
				const nextRow = (event.target as HTMLElement).parentElement?.nextElementSibling;
				if (nextRow) (nextRow.children[0] as HTMLElement).focus();
				break;
			case 'ArrowUp':
				// Move to the previous row
				const prevRow = (event.target as HTMLElement).parentElement?.previousElementSibling;
				if (prevRow) (prevRow.children[0] as HTMLElement).focus();
				break;
		}
	}

	function swipeFunc(container, parentElement) {
		var listener = SwipeListener(container);
		container.addEventListener('swipe', function (e) {
			var directions = e.detail.directions;
			if (directions.left) {
				const buttonContainer = parentElement.querySelector('#button-container') as HTMLElement;
				buttonContainer.style.display = 'flex';
			}
			if (directions.right) {
				const buttonContainer = parentElement.querySelector('#button-container') as HTMLElement;
				buttonContainer.style.display = 'none';
			}
		});
	}

	onMount(() => {
		const swiper = document.querySelectorAll('#swipe-container');
		swiper.forEach((element: HTMLElement) => {
			const parentElement = element.parentElement; // Get the parent element
			swipeFunc(element, parentElement);
		});
	});
</script>

{#if !screenSize.isMobile}
	<tr
		tabindex="0"
		on:keydown={handleKeydown}
		class={cn(
			'bg-white hover:bg-gray-50',
			classes,
			{
				'bg-blue-100 text-black': $selectedRows.has(row[keyField]),
				'border-b': bordered
			},
			trStyle
		)}
		draggable={row.draggable || isDraggable}
		on:dragstart={(event) => handleDragStart(index)}
		on:dragover={(event) => event.preventDefault()}
		on:drop={(event) => handleDrop(index)}
		on:click={() => row['collapse-able'] && toggleRowExpansion(row[keyField])}
	>
		{#if hasCheckBox}
			<td
				tabindex="0"
				on:keydown={handleKeydown}
				class={cn('!p-4', tdStyle)}
				on:click={handleCheckboxChange}
			>
				<Checkbox
					id={`checkbox-${row[keyField]}`}
					on:change={() => toggleRowSelection(row[keyField])}
					checked={$selectedRows.has(row[keyField])}
					role="checkbox"
				/>
			</td>
		{/if}

		{#if hasRadioButton}
			<td
				tabindex="0"
				on:keydown={handleKeydown}
				class={cn('!p-4', tdStyle)}
				on:click={handleCheckboxChange}
			>
				<Radio
					name="table-radio-button"
					on:change={() => toggleRowSelectionRadio(row[keyField])}
					checked={$selectedRows.has(row[keyField])}
				/>
			</td>
		{/if}

		<!-- {#if row.draggable || isDraggable}
		<td class={cn('!p-4', tdStyle)} tabindex="0" on:keydown={handleKeydown}>
			<img class="m-auto" src={'/assets/svg/sort-icon.svg'} alt="" />
		</td>
	{/if} -->

		<!-- Main columns visible based on screen size -->
		{#each columns as column}
			{#if !screenSize.isMobile || !mobileView.includes(column.key)}
				<td
					tabindex="0"
					on:keydown={handleKeydown}
					class={cn(
						'px-6 py-2',
						{
							'border-r': bordered
						},
						tdStyle
					)}
				>
					{#if column.customRender}
						{@html column.customRender(row[column.key])}
					{:else}
						{row[column.key]}
					{/if}
				</td>
			{/if}
		{/each}

		{#if hasActions}
			<td
				tabindex="0"
				on:keydown={handleKeydown}
				class={cn('!p-4', tdStyle, {
					'border-r': bordered
				})}
				on:click={handleDropdownClick}
			>
				<div class="relative">
					<DotsHorizontalOutline
						data-testid="dropdown-menu"
						triggeredBy=".dots-menu"
						class="dots-menu dark:text-white"
					/>
					<Dropdown class="min-w-[200px] shadow-none" triggeredBy=".dots-menu">
						{#if actionsContent?.items}
							{#each actionsContent.items as item}
								<DropdownItem on:click={item.action}>{item.label}</DropdownItem>
							{/each}
						{:else}
							<DropdownItem>Edit</DropdownItem>
							<DropdownItem>Delete</DropdownItem>
							<DropdownItem>View</DropdownItem>
						{/if}
					</Dropdown>
				</div>
			</td>
		{/if}
	</tr>
{/if}

{#if screenSize.isMobile && mobileView.length > 0}
	<tr class={cn('bg-white', trStyle)}>
		<td
			colspan={columns.length +
				(hasCheckBox ? 1 : 0) +
				(hasRadioButton ? 1 : 0) +
				(hasActions ? 1 : 0)}
			class="pt-2 pb-0"
		>
			<div class="wrapper">
				<div id={`swipe-container`} data-testid="swipe-container" class="swipe-container">
					<div class="min-h-[80px] w-full">
						<div class="flex w-full items-center justify-between">
							<!-- Left section -->
							<div class="flex items-center space-x-3">
								{#each columns as column, key}
									{#if mobileView.includes(column.key) && key === 0}
										{#if column.key === 'RosterPerformer'}
											<div class="flex items-center gap-3">
												<svg
													width="8"
													height="8"
													viewBox="0 0 8 8"
													fill="none"
													xmlns="http://www.w3.org/2000/svg"
													class="cursor-move"
												>
													<circle cx="2" cy="2" r="1" fill="#666C79" />
													<circle cx="6" cy="2" r="1" fill="#666C79" />
													<circle cx="2" cy="6" r="1" fill="#666C79" />
													<circle cx="6" cy="6" r="1" fill="#666C79" />
												</svg>
												<img
													src={row[column.key].User.performerProfile.avatarPosition1 ||
														'/default-avatar.jpg'}
													alt={row[column.key].User.performerProfile.stageName ||
														`${row[column.key].User.performerProfile.firstName} ${row[column.key].User.performerProfile.lastName}`}
													class="h-16 w-16 rounded-[6px] object-cover"
												/>
												<div class="flex flex-col">
													<span class="max-w-[200px] truncate text-gray-900">
														{row[column.key].User.performerProfile.stageName ||
															`${row[column.key].User.performerProfile.firstName} ${row[column.key].User.performerProfile.lastName}`}
													</span>
													{#each columns as roleColumn}
														{#if roleColumn.key === 'roleOnStage' && mobileView.includes(roleColumn.key)}
															<div class="mt-1">
																{#if row[roleColumn.key] === 0}
																	<span
																		class="inline-flex items-center justify-center rounded-md bg-[#ADB0B7] px-4 py-1 text-sm font-medium text-white"
																	>
																		HOST
																	</span>
																{:else if row[roleColumn.key] === 1}
																	<span
																		class="inline-flex items-center justify-center rounded-md bg-[#38A0FF] px-4 py-1 text-sm font-medium text-white"
																	>
																		FEATURE
																	</span>
																{:else if row[roleColumn.key] === 2}
																	<span
																		class="inline-flex items-center justify-center rounded-md bg-[#FF6666] px-4 py-1 text-sm font-medium text-white"
																	>
																		HEADLINER
																	</span>
																{:else if row[roleColumn.key] === 3}
																	<span
																		class="inline-flex items-center justify-center rounded-md bg-[#00AC87] px-4 py-1 text-sm font-medium text-white"
																	>
																		GUEST
																	</span>
																{/if}
															</div>
														{/if}
													{/each}
												</div>
											</div>
										{/if}
									{/if}
								{/each}
							</div>

							<!-- Right section -->
							<div class="flex flex-col items-end space-x-4">
								<!-- Set number -->
								<div class="mb-1">
									<span class="text-black">Set: {row.setNumber || 10}</span>
								</div>

								<!-- Status -->
								{#each columns as column}
									{#if column.key === 'acceptedState' && mobileView.includes(column.key)}
										{#if row[column.key] === 0}
											<div class="flex justify-center">
												<span
													class="inline-flex items-center justify-center rounded-[6px] bg-gray-100 px-4 py-1 text-xs font-medium text-gray-800"
												>
													<img
														class="mr-1 h-4 w-4"
														src="src/assets/svg/edit-user-02.png"
														alt="edit"
													/>
													Invited
												</span>
											</div>
										{:else if row[column.key] === 1}
											<div class="flex justify-center">
												<span
													class="inline-flex items-center justify-center rounded-[6px] bg-yellow-100 px-4 py-1 text-xs font-medium text-yellow-800"
												>
													Pending
												</span>
											</div>
										{:else if row[column.key] === 2}
											<div class="flex justify-center">
												<span
													class="inline-flex items-center justify-center rounded-[6px] bg-green-100 px-4 py-1 text-xs font-medium text-green-800"
												>
													<img
														class="mr-1 h-4 w-4"
														src="src/assets/svg/tick-circle.png"
														alt="tick"
													/>
													Confirmed
												</span>
											</div>
										{:else if row[column.key] === 3}
											<div class="flex justify-center">
												<span
													class="inline-flex items-center justify-center rounded-[6px] bg-[#FF922E26] px-4 py-1 text-xs font-medium text-[#FF922E]"
												>
													<img class="mr-1 h-4 w-4" src="src/assets/svg/pin.svg" alt="pin" />
													Pinned
												</span>
											</div>
										{:else if row[column.key] === 4}
											<div class="flex justify-center">
												<span
													class="inline-flex items-center justify-center rounded-[6px] bg-[#FF666626] px-4 py-1 text-xs font-medium text-[#FF6666]"
												>
													<img class="mr-1 h-4 w-4" src="src/assets/svg/cancel.svg" alt="cancel" />
													Declined
												</span>
											</div>
										{/if}
									{/if}
								{/each}
							</div>
						</div>
					</div>
				</div>
			</div>
		</td>
	</tr>
{/if}
