<script lang="ts">
    function shortId() {
        return Math.random().toString(36).substring(2, 8);
    }

    let timers = $state([
        { id: shortId(), title: 'Sample Timer', creator: 'Alice', duration: 30, remaining: 30, status: 'green', watchers: 5, created: new Date().toISOString() },
    ]);
    let countdowns = $state([
        { id: shortId(), title: 'Sample Countdown', creator: 'Bob', target: '2025-09-06T20:00', diff: 1, status: 'yellow', watchers: 3, created: new Date().toISOString() }
    ]);

    let timerForm = $state({ title: '', duration: '' });
    let countdownForm = $state({ title: '', target: '' });
    let user = $state('User' + Math.floor(Math.random() * 1000));
    let timerSortOption = $state('created-desc'); // Default sort option for timers
    let countdownSortOption = $state('created-desc'); // Default sort option for countdowns

    let displayedTimers = $derived([...timers]);
    let displayedCountdowns = $derived([...countdowns]);


    function addTimer(e: Event) {
        e.preventDefault();
        if (!timerForm.title || !timerForm.duration) return;
        timers = [
            ...timers,
            {
                id: shortId(),
                title: timerForm.title,
                creator: user,
                duration: Number(timerForm.duration),
                remaining: Number(timerForm.duration),
                status: 'green',
                watchers: 1,
                created: new Date().toISOString()
            }
        ];
        updateTimerSort(timerSortOption);
        timerForm = { title: '', duration: '' };
    }


    function addCountdown(e: Event) {
        e.preventDefault();
        if (!countdownForm.title || !countdownForm.target) return;
        countdowns = [
            ...countdowns,
            {
                id: shortId(),
                title: countdownForm.title,
                creator: user,
                target: countdownForm.target,
                status: 'yellow',
                watchers: 1,
                diff: Math.floor((new Date(countdownForm.target).getTime() - Date.now()) / 1000),
                created: new Date().toISOString()
            }
        ];
        updateCountdownSort(countdownSortOption);
        countdownForm = { title: '', target: '' };
    }

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

    function formatDateTime(dateTime: string): string {
        const options: Intl.DateTimeFormatOptions = {
            weekday: 'short',
            year: 'numeric',
            month: 'short',
            day: 'numeric',
            hour: '2-digit',
            minute: '2-digit',
            hour12: true
        };
        return new Date(dateTime).toLocaleString('en-US', options);
    }

    function openTimer(id: string) {
        window.location.href = `/timer/${id}`;
    }

    function openCountdown(id: string) {
        window.location.href = `/countdown/${id}`;
    }

    function sortItems<T extends { id: string; title: string; creator: string; created: string; remaining?: number; status: string; watchers: number; target?: string; diff?: number }>(
        items: T[],
        option: string
    ): T[] {
        switch (option) {
            case 'created-asc':
                return [...items].sort((a, b) => new Date(a.created).getTime() - new Date(b.created).getTime());
            case 'created-desc':
                return [...items].sort((a, b) => new Date(b.created).getTime() - new Date(a.created).getTime());
            case 'remaining-asc':
                return [...items].sort((a, b) => (a.remaining || 0) - (b.remaining || 0));
            case 'remaining-desc':
                return [...items].sort((a, b) => (b.remaining || 0) - (a.remaining || 0));
            case 'title-asc':
                return [...items].sort((a, b) => a.title.localeCompare(b.title));
            case 'title-desc':
                return [...items].sort((a, b) => b.title.localeCompare(a.title));
            default:
                return items;
        }
    }

    function updateTimerSort(option: string): void {
        timerSortOption = option;
        displayedTimers = sortItems(timers, option);
    }

    function updateCountdownSort(option: string): void {
        countdownSortOption = option;
        displayedCountdowns = sortItems(countdowns, option);
    }

    function getSortLabel(option: string): string {
        switch (option) {
            case 'created-desc': return 'Newest';
            case 'created-asc': return 'Oldest';
            case 'remaining-asc': return 'Shortest Remaining';
            case 'remaining-desc': return 'Longest Remaining';
            case 'title-asc': return 'Title A-Z';
            case 'title-desc': return 'Title Z-A';
            default: return 'Sort Options';
        }
    }

    $effect(() => {
        displayedTimers = sortItems([...timers], timerSortOption);
    });

    $effect(() => {
        displayedCountdowns = sortItems([...countdowns], countdownSortOption);
    });


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
            <h3 style="display: flex; justify-content: space-between; align-items: center;">
                Active Timers
                <!-- Move dropdown to the right side of the titles and adjust its appearance -->
                <div class="dropdown">
                    <button class="dropdown-toggle">{getSortLabel(timerSortOption)}</button>
                    <div class="dropdown-content">
                        <button onclick={() => updateTimerSort('created-desc')}>Newest</button>
                        <button onclick={() => updateTimerSort('created-asc')}>Oldest</button>
                        <button onclick={() => updateTimerSort('remaining-asc')}>Shortest Remaining</button>
                        <button onclick={() => updateTimerSort('remaining-desc')}>Longest Remaining</button>
                        <button onclick={() => updateTimerSort('title-asc')}>Title A-Z</button>
                        <button onclick={() => updateTimerSort('title-desc')}>Title Z-A</button>
                    </div>
                </div>
            </h3>
            <table>
                <tbody>
                    {#each displayedTimers as t}
                    <tr>
                        <td style="width:100%;padding:0;border:none;">
                            <button type="button" class="row-box" style="width:100%;text-align:left;" onclick={() => openTimer(t.id)} aria-label={`Open timer ${t.title}`}> 
                                <span style="flex:2;">{t.title}</span>
                                <span class="centered" style="flex:1;">{t.creator}</span>
                                <span class="centered" style="flex:1;">{formatTime(t.remaining)}</span>
                                <span class="centered" style="flex:0.5;"><span class="status-dot {t.status}"></span></span>
                                <span class="centered" style="flex:0.7;">{t.watchers}🧑</span>
                            </button>
                        </td>
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
            <h3 style="display: flex; justify-content: space-between; align-items: center;">
                Active Countdowns
                <!-- Move dropdown to the right side of the titles and adjust its appearance -->
                <div class="dropdown">
                    <button class="dropdown-toggle">{getSortLabel(countdownSortOption)}</button>
                    <div class="dropdown-content">
                        <button onclick={() => updateCountdownSort('created-desc')}>Newest</button>
                        <button onclick={() => updateCountdownSort('created-asc')}>Oldest</button>
                        <button onclick={() => updateCountdownSort('remaining-asc')}>Shortest Remaining</button>
                        <button onclick={() => updateCountdownSort('remaining-desc')}>Longest Remaining</button>
                        <button onclick={() => updateCountdownSort('title-asc')}>Title A-Z</button>
                        <button onclick={() => updateCountdownSort('title-desc')}>Title Z-A</button>
                    </div>
                </div>
            </h3>
            <table>
                <tbody>
                    {#each displayedCountdowns as c}
                    <tr>
                        <td style="width:100%;padding:0;border:none;">
                            <button type="button" class="row-box" style="width:100%;text-align:left;" onclick={() => openCountdown(c.id)} aria-label={`Open countdown ${c.title}`}> 
                                <span style="flex:2;">{c.title}</span>
                                <span class="centered" style="flex:1;">{c.creator}</span>
                                <span class="centered" style="flex:1;">{formatDateTime(c.target)}</span>
                                <span class="centered" style="flex:1;">{formatTime(c.diff ?? Math.floor((new Date(c.target).getTime() - Date.now())/1000))}</span>
                                <span class="centered" style="flex:0.5;"><span class="status-dot {c.status}"></span></span>
                                <span class="centered" style="flex:0.7;">{c.watchers}🧑</span>
                            </button>
                        </td>
                    </tr>
                    {/each}
                </tbody>
            </table>
        </div>    
    </section>
</div>