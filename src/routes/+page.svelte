<script lang="ts">
    // ...existing code...
</script>

<h1 style="text-align: center;">Timer Screen</h1>

<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; width: 100vw; min-height: 100vh">

    <!-- Timer Column -->
    <section style="padding: 2rem;">
        <h2>Timers</h2>
        <!-- New Timer Form -->
        <div style="border: 1px solid #ccc; padding: 1rem; margin-bottom: 2rem; border-radius: 8px;">
            <h3>Create New Timer</h3>
            <label>
                Title:
                <input type="text" placeholder="Timer Title" />
            </label>
            <br />
            <label>
                Duration (seconds):
                <input type="number" min="1" placeholder="Duration" />
            </label>
            <br />
            <button>Start Timer</button>
        </div>
        <!-- Active Timers List -->
        <div style="border: 1px solid #ccc; padding: 1rem; border-radius: 8px;">
            <h3>Active Timers</h3>
            <table style="width: 100%;">
                <thead>
                    <tr>
                        <th>Title</th>
                        <th>Creator</th>
                        <th>Time Remaining</th>
                        <th>Status</th>
                        <th>Watchers</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Example row -->
                    <tr>
                        <td>Sample Timer</td>
                        <td>Alice</td>
                        <td>00:01:23</td>
                        <td>⏳</td>
                        <td>5</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </section>

    <!-- Countdown Column -->
    <section style="padding: 2rem;">
        <h2>Countdowns</h2>
        <!-- New Countdown Form -->
        <div style="border: 1px solid #ccc; padding: 1rem; margin-bottom: 2rem; border-radius: 8px;">
            <h3>Create New Countdown</h3>
            <label>
                Title:
                <input type="text" placeholder="Countdown Title" />
            </label>
            <br />
            <label>
                Date & Time:
                <input type="datetime-local" />
            </label>
            <br />
            <button>Start Countdown</button>
        </div>
        <!-- Active Countdowns List -->
        <div style="border: 1px solid #ccc; padding: 1rem; border-radius: 8px;">
            <h3>Active Countdowns</h3>
            <table style="width: 100%;">
                <thead>
                    <tr>
                        <th>Title</th>
                        <th>Creator</th>
                        <th>Date & Time</th>
                        <th>Time Remaining</th>
                        <th>Status</th>
                        <th>Watchers</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Example row -->
                    <tr>
                        <td>Sample Countdown</td>
                        <td>Bob</td>
                        <td>2025-09-05 12:00</td>
                        <td>01:23:45</td>
                        <td>⏳</td>
                        <td>3</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </section>

</div>