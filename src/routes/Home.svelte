<script>
import { onMount } from "svelte";

	let active = false;
	let isStarted = false;
	let workTime = 25;
	let shortBreak = 5;
	let longBreak = 20;
	let loopsPerLongBreak = 3;
	let currentTime;
	let currentDisplayTime = '';
	let onBreak = false;
	let loop = 1;
	let task;
	let runText = '';
	const notification = new Audio();

	const run = () => {
		notification.src = './Notification.m4a';
		if (!active) {
			currentTime = isStarted ? currentTime : workTime * 60;
			active = true;
			task = setInterval(countdown, 1000);
			isStarted = true;
			runText = getRunText();
		} else {
			clearInterval(task);
			active = false;
		}
	}

	const countdown = () => {
		currentTime--;
		if (currentTime === 5) {
			notification.play();
		}
		if (currentTime === 0) {
			onBreak = !onBreak;
			if (!onBreak) {
				loop++;
				currentTime = workTime * 60;
			} else {
				currentTime = loop % (loopsPerLongBreak + 1) === 0 ? longBreak * 60 : shortBreak * 60;
			}
			runText = getRunText();
		}
		setDisplayTime(currentTime );
	}

	const clear = () => {
		workTime = 25;
		shortBreak = 5;
		longBreak = 20;
		loopsPerLongBreak = 3;
		loop = 1;
		active = false;
		isStarted = false;
		setDisplayTime(workTime * 60)
		runText = '';
	}

	const setDisplayTime = (time) => {
        currentDisplayTime = (new Date(time*1000)).toISOString().slice(11,19);
    }

	const getRunText = () => {
		if (onBreak) {
			if (loop % (loopsPerLongBreak + 1)) { // we're not on long break
				const shortLoops = loop - (Math.floor(loop / (loopsPerLongBreak + 1)));
				return `Short Break #${shortLoops}`;
			} else {
				return `Long Break #${loop / (loopsPerLongBreak + 1)}`;
			}
		} else {
			return `Working session #${loop}`;
		}
	}

	onMount(() => {
		setDisplayTime(workTime * 60);
	});

	$: workTime, setDisplayTime(workTime * 60);
</script>

<main>
	<div class="section">
		<div class="full-height">
			<div class="container">
				<div class="columns is-centered is-vcentered has-text-centered">
					<div class="column is-8">
						<div class="section" id="title">
							<h1 class="title is-1 has-text-white">Pomodoro Timer</h1>
							<h2 class="subtitle is-3 has-text-white">Keep yourself focused!</h2>
						</div>
						<div class="box">
							<div class="section">
								<div class="columns is-centered is-vcentered is-multiline has-text-centered is-mobile">
									<div class="column is-full has-text-centered">
										<h1 class="title">{runText}</h1>
										<h1 class="timer title is-1 has-text-dark">
											{currentDisplayTime}
										</h1>
									</div>
									{#if !active}
									<div class="column is-one-third has-text-right">
										<strong>Working Session</strong>
									</div>
									<div class="column is-two-thirds">
										<div class="field has-addons">
											<p class="control">
											<input class="input" bind:value="{workTime}" type="number" placeholder="25" disabled={isStarted}>
											</p>
											<p class="control">
												<!-- svelte-ignore a11y-missing-attribute -->
												<a class="button is-static">
													Minutes
												</a>
											</p>
										</div>
									</div>
									<div class="column is-one-third has-text-right">
										<strong>Short Break</strong>
									</div>
									<div class="column is-two-thirds">
										<div class="field has-addons">
											<p class="control">
											<input class="input" bind:value="{shortBreak}" type="number" placeholder="5" disabled={isStarted}>
											</p>
											<p class="control">
												<!-- svelte-ignore a11y-missing-attribute -->
												<a class="button is-static">
													Minutes
												</a>
											</p>
										</div>
									</div>
									<div class="column is-one-third has-text-right">
										<strong>Long Break</strong>
									</div>
									<div class="column is-two-thirds">
										<div class="field has-addons">
											<p class="control">
											<input class="input" bind:value="{longBreak}" type="number" placeholder="20" disabled={isStarted}>
											</p>
											<p class="control">
												<!-- svelte-ignore a11y-missing-attribute -->
												<a class="button is-static">
													Minutes
												</a>
											</p>
										</div>
									</div>
									<div class="column is-one-third has-text-right">
										<strong>Long Break Every</strong>
									</div>
									<div class="column is-two-thirds">
										<div class="field has-addons">
											<p class="control">
											<input class="input" bind:value="{loopsPerLongBreak}" type="number" placeholder="3" disabled={isStarted}>
											</p>
											<p class="control">
												<!-- svelte-ignore a11y-missing-attribute -->
												<a class="button is-static">
													Sessions
												</a>
											</p>
										</div>
									</div>
									<div class="field is-grouped mt-3">
										<p class="control">
											<button class="button is-primary" on:click="{run}">Start</button>
											<button class="button is-warning" on:click="{clear}" disabled={!isStarted}>Reset</button>
										</p>
									</div>	
									{:else}
										<div class="field is-grouped mt-3">
											<p class="control">
												<button class="button is-primary" on:click="{run}">Pause</button>
											</p>
										</div>
									{/if}
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</main>

<style>
	#title > .title, #title > .subtitle {
		text-shadow: 2px 2px rgb(46, 45, 45);
	}

</style>