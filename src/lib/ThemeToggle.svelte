<script lang="ts">
	type Theme = 'default' | 'retro' | 'cyberpunk' | 'valentine' | 'aqua';

	let isOpen = false;

	function clickOutside(node: HTMLElement) {
		const handleClick = (event: MouseEvent) => {
			if (node && event.target instanceof Node && !node.contains(event.target)) {
				isOpen = false;
			}
		};

		document.addEventListener('click', handleClick, true);

		return {
			destroy() {
				document.removeEventListener('click', handleClick, true);
			}
		};
	}

	function handleThemeChange(event: Event) {
		if (event.target instanceof HTMLInputElement) {
			const selectedTheme = event.target.value as Theme;
			document.documentElement.setAttribute('data-theme', selectedTheme);
			// Removed the isOpen = false line so it stays open after selection
		}
	}
</script>

<!-- svelte-ignore a11y_no_noninteractive_tabindex -->
<div class="dropdown" use:clickOutside>
	<div tabindex="0" role="button" class="btn m-1" on:click={() => (isOpen = !isOpen)}>
		Theme
		<svg
			width="12px"
			height="12px"
			class="inline-block h-2 w-2 fill-current opacity-60"
			xmlns="http://www.w3.org/2000/svg"
			viewBox="0 0 2048 2048"
		>
			<path d="M1799 349l242 241-1017 1017L7 590l242-241 775 775 775-775z"></path>
		</svg>
	</div>

	<!-- svelte-ignore a11y_no_noninteractive_tabindex -->
	<ul
		tabindex="0"
		class="dropdown-content bg-base-300 rounded-box z-[1] w-52 p-2 shadow-2xl"
		class:hidden={!isOpen}
	>
		<li>
			<input
				type="radio"
				name="theme-dropdown"
				class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
				aria-label="Default"
				value="default"
				on:change={handleThemeChange}
			/>
		</li>
		<li>
			<input
				type="radio"
				name="theme-dropdown"
				class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
				aria-label="Retro"
				value="retro"
				on:change={handleThemeChange}
			/>
		</li>
		<li>
			<input
				type="radio"
				name="theme-dropdown"
				class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
				aria-label="Cyberpunk"
				value="cyberpunk"
				on:change={handleThemeChange}
			/>
		</li>
		<li>
			<input
				type="radio"
				name="theme-dropdown"
				class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
				aria-label="Valentine"
				value="valentine"
				on:change={handleThemeChange}
			/>
		</li>
		<li>
			<input
				type="radio"
				name="theme-dropdown"
				class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
				aria-label="Aqua"
				value="aqua"
				on:change={handleThemeChange}
			/>
		</li>
	</ul>
</div>
