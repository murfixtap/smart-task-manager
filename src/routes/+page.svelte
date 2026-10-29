<script lang="ts">
	import { Trash, Plus, Check } from '@lucide/svelte';
	import { Button, DropdownMenu } from 'bits-ui';

	type Task = {
		id: number;
		title: string;
		done: boolean;
		createdAt: number;
	};

	let tasks = $state<Task[]>([
		{ id: 1, title: 'Задача 1', done: true, createdAt: 1793162730819 },
		{ id: 2, title: 'Задача 2', done: false, createdAt: 1793162913212 },
		{ id: 3, title: 'Задача 3', done: false, createdAt: 1793162920335 },
		{ id: 4, title: 'Задача 4', done: true, createdAt: 1793162940174 },
		{ id: 5, title: 'Задача 5', done: false, createdAt: 1793162946380 }
	]);
	let newTitle = $state<string>('');
	let nextId = $state<number>(6);

	type Filter = 'Все' | 'Готовые' | 'Активные';
	let filter = $state<Filter>('Все');
	const visible = $derived(
		filter === 'Все'
			? tasks
			: tasks.filter((task) => (filter === 'Готовые' ? task.done : !task.done))
	);

	function addTask() {
		const title: string = newTitle.trim();
		if (!title) return;

		tasks.push({
			id: nextId,
			title,
			done: false,
			createdAt: Date.now()
		});

		nextId++;
		newTitle = '';
	}

	function toggleTask(task: Task) {
		task.done = !task.done;
	}

	function deleteTask(id: number) {
		tasks = tasks.filter((t) => t.id !== id);
	}
</script>

<svelte:head>
	<title>Smart Task</title>
</svelte:head>

<main class="p-4">
	<div class="mx-auto flex w-full max-w-3xl flex-col gap-12 pt-6">
		<div class="flex flex-col gap-4">
			<!-- Форма для создания новой задачи -->
			<input
				bind:value={newTitle}
				onkeydown={(e) => e.key === 'Enter' && addTask()}
				aria-label="Новая задача"
				type="text"
				placeholder="Что нужно сделать?"
				class="rounded-lg border-border transition focus:ring-border focus:outline-none"
			/>
			<Button.Root
				onclick={addTask}
				class="flex cursor-pointer items-center justify-center gap-1 rounded-lg bg-primary py-2 font-medium text-primary-foreground transition hover:bg-primary/80"
				><Plus class="stroke-3" size="16px" width="16px" height="16px" />Добавить задачу
			</Button.Root>
		</div>

		<div class="grid gap-4">
			<div>
				<DropdownMenu.Root>
					<DropdownMenu.Trigger
						class="inline-flex h-10 w-fit cursor-pointer items-center justify-center rounded-xl border border-border bg-surface px-4 text-sm font-medium text-primary select-none active:scale-[0.98]"
					>
						<div class="flex gap-1.5">
							<p>Фильтр</p>
							<span>></span>
							<p>{filter}</p>
						</div>
					</DropdownMenu.Trigger>
					<DropdownMenu.Portal>
						<DropdownMenu.Content
							class="z-50 w-40 rounded-xl border border-border bg-surface px-1 py-1.5 outline-hidden focus-visible:outline-hidden"
							sideOffset={8}
							align="start"
						>
							<DropdownMenu.Item
								onclick={() => (filter = 'Все')}
								class="flex h-10 items-center rounded-lg p-3 text-sm font-medium ring-0! ring-transparent! select-none focus-visible:outline-none data-highlighted:bg-muted/20"
							>
								<div class="flex gap-1">
									<p>Все</p>
									<span class="text-muted/80">·</span>
									<p>{tasks.length}</p>
								</div>
							</DropdownMenu.Item>
							<DropdownMenu.Item
								onclick={() => (filter = 'Готовые')}
								class="flex h-10 items-center rounded-lg p-3 text-sm font-medium ring-0! ring-transparent! select-none focus-visible:outline-none data-highlighted:bg-muted/20"
							>
								<div class="flex gap-1">
									<p>Готовые</p>
								</div>
							</DropdownMenu.Item>
							<DropdownMenu.Item
								onclick={() => (filter = 'Активные')}
								class="flex h-10 items-center rounded-lg p-3 text-sm font-medium ring-0! ring-transparent! select-none focus-visible:outline-none data-highlighted:bg-muted/20"
							>
								<div class="flex gap-1">
									<p>Активные</p>
								</div>
							</DropdownMenu.Item>
						</DropdownMenu.Content>
					</DropdownMenu.Portal>
				</DropdownMenu.Root>
			</div>

			<!-- Список задач -->
			<section class="grid gap-4 sm:grid-cols-2">
				{#each visible as task (task.id)}
					<div
						class="flex flex-col justify-between gap-2 rounded-xl bg-surface p-4 {task.done
							? 'border-2 border-green-500/30'
							: 'border-2 border-red-500/30'}"
					>
						<!-- Заголовок задачи и его номер -->
						<div class="flex justify-between">
							{#if !task.done}
								<p class="cursor-pointer">{task.title}</p>
							{:else}
								<p class="cursor-pointer line-through opacity-60">{task.title}</p>
							{/if}
							<span class="font-medium text-muted">#{task.id}</span>
						</div>

						<div class="flex h-fit justify-end gap-2">
							<!-- Пометить задачу как выполненной или наоборот -->
							<Button.Root
								onclick={() => toggleTask(task)}
								class="cursor-pointer rounded-lg p-1 transition hover:ring-1 hover:ring-border"
								><Check class="stroke-2 text-primary" size="20px" width="20px" height="20px" />
							</Button.Root>

							<!-- Удалить задачу -->
							<Button.Root
								onclick={() => deleteTask(task.id)}
								class="cursor-pointer rounded-lg p-1 transition hover:ring-1 hover:ring-border"
								><Trash class="stroke-2 text-primary" size="20px" width="20px" height="20px" />
							</Button.Root>
						</div>
					</div>
				{/each}
			</section>
		</div>
	</div>
</main>
