<script lang="ts">
    let timers = $state([
        { id: 1, name: "1 Minute", duration: 60, isRunning: false, end: 65435453445 },
        { id: 2, name: "2 Minutes", duration: 120, isRunning: false, end: 65435453445 }
    ]);

    let answer : string = $state('');
    // let selected : string = $state();

    function addTimer(e: Event) {
        e.preventDefault();

		// alert(
		// 	`answered question ${selected.id} (${selected.name}) with "${answer}"`
		// );
    }

</script>

<h1>Synced Timer</h1>

<form onsubmit={addTimer}>
    <label for="timer">Add Timer</label>
    <input bind:value={answer} />

    <button type="submit">Add</button>


</form>