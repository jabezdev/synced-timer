<script lang="ts">
// Svelte 5 $state for local state management
let timers = $state([
    { title: 'Sample Timer', creator: 'Alice', duration: 83, remaining: 83, status: 'green', watchers: 5 },
]);
let countdowns = $state([
    { title: 'Sample Countdown', creator: 'Bob', target: '2025-09-05T12:00', diff: 1, status: 'yellow', watchers: 3 }
]);

let timerForm = $state({ title: '', duration: '' });
let countdownForm = $state({ title: '', target: '' });
let user = $state('User' + Math.floor(Math.random() * 1000));

function addTimer(e: Event) {
    e.preventDefault();
    if (!timerForm.title || !timerForm.duration) return;
    timers = [
        ...timers,
        {
            title: timerForm.title,
            creator: user,
            duration: Number(timerForm.duration),
            remaining: Number(timerForm.duration),
            status: 'green',
            watchers: 1
        }
    ];
    timerForm = { title: '', duration: '' };
}

function addCountdown(e: Event) {
    e.preventDefault();
    if (!countdownForm.title || !countdownForm.target) return;
    countdowns = [
        ...countdowns,
        {
            title: countdownForm.title,
            creator: user,
            target: countdownForm.target,
            status: 'yellow',
            watchers: 1,
            diff: Math.floor((new Date(countdownForm.target).getTime() - Date.now()) / 1000)
        }
    ];
    countdownForm = { title: '', target: '' };
}

// Timer countdown logic
let timerInterval = setInterval(() => {
    timers = timers.map(t => {
        if (t.remaining > 0) {
            const next = { ...t, remaining: t.remaining - 1 };
            if (next.remaining === 0) next.status = 'red';
            return next;
        }
        return t;
    });
}, 1000);

// Countdown logic
let countdownInterval = setInterval(() => {
    countdowns = countdowns.map(c => {
        const now = new Date();
        const target = new Date(c.target);
        const diff = Math.floor((target.getTime() - now.getTime()) / 1000);
        let status = c.status;
        if (diff <= 0) status = 'red';
        else if (diff < 60 * 10) status = 'yellow';
        else status = 'green';
        return { ...c, diff, status };
    });
}, 1000);

function formatTime(s: number) {
    if (s < 0) s = 0;
    const h = Math.floor(s / 3600).toString().padStart(2, '0');
    const m = Math.floor((s % 3600) / 60).toString().padStart(2, '0');
    const sec = (s % 60).toString().padStart(2, '0');
    return `${h}:${m}:${sec}`;
}
</script>

<h1>Timer Screen</h1>

<div class="main-grid">
    <!-- Timer Column -->
    <section class="section-padding">
        <h2>Timers</h2>
        <!-- New Timer Form -->
        <div class="box">
            <h3>Create New Timer</h3>
            <form onsubmit={addTimer}>
                <label>
                    Title:
                    <input type="text" placeholder="Timer Title" bind:value={timerForm.title} />
                </label>
                <br />
                <label>
                    Duration (seconds):
                    <input type="number" min="1" placeholder="Duration" bind:value={timerForm.duration} />
                </label>
                <br />
                <button type="submit">Start Timer</button>
            </form>
        </div>
        <!-- Active Timers List -->
        <div class="box">
            <h3>Active Timers</h3>
            <table>
                <tbody>
                    {#each timers as t}
                    <tr>
                        <td>{t.title}</td>
                        <td class="centered">{t.creator}</td>
                        <td class="centered">{formatTime(t.remaining)}</td>
                        <td class="centered">
                            <span class="status-dot {t.status}"></span>
                        </td>
                        <td class="centered">{t.watchers}🧑</td>
                    </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </section>

    <!-- Countdown Column -->
    <section class="section-padding">
        <h2>Countdowns</h2>
        <!-- New Countdown Form -->
        <div class="box">
            <h3>Create New Countdown</h3>
            <form onsubmit={addCountdown}>
                <label>
                    Title:
                    <input type="text" placeholder="Countdown Title" bind:value={countdownForm.title} />
                </label>
                <br />
                <label>
                    Date & Time:
                    <input type="datetime-local" bind:value={countdownForm.target} />
                </label>
                <br />
                <button type="submit">Start Countdown</button>
            </form>
        </div>
        <!-- Active Countdowns List -->
        <div class="box">
            <h3>Active Countdowns</h3>
            <table>
                <tbody>
                    {#each countdowns as c}
                    <tr>
                        <td>{c.title}</td>
                        <td class="centered">{c.creator}</td>
                        <td class="centered">{c.target.replace('T', ' ').slice(0, 16)}</td>
                        <td class="centered">{formatTime(c.diff ?? Math.floor((new Date(c.target).getTime() - Date.now())/1000))}</td>
                        <td class="centered">
                            <span class="status-dot {c.status}"></span>
                        </td>
                        <td class="centered">{c.watchers}🧑</td>
                    </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </section>
</div>