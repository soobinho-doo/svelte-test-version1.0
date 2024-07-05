<script lang="ts">
    // 현재 날짜 가져오기
    let date: Date = new Date();
    let year: number = date.getFullYear();
    let month: number = date.getMonth(); // 0: January

    const dayNames: string[] = ['일', '월', '화', '수', '목', '금', '토'];

    function getFirstDayOfMonth(year: number, month: number): Date {
        return new Date(year, month, 1);
    }

    function getLastDayOfMonth(year: number, month: number): Date {
        return new Date(year, month + 1, 0);
    }

    function getDayOfWeek(date: Date): number {
        return date.getDay(); // 0 (Sunday) ~ 6 (Saturday)
    }

    function getPreviousMonth(year: number, month: number): [number, number] {
        return month === 0 ? [year - 1, 11] : [year, month - 1];
    }

    function getNextMonth(year: number, month: number): [number, number] {
        return month === 11 ? [year + 1, 0] : [year, month + 1];
    }

    function previousMonth(): void {
        [year, month] = getPreviousMonth(year, month);
    }

    function nextMonth(): void {
        [year, month] = getNextMonth(year, month);
    }

    let firstDayOfWeek: number;
    let daysInMonth: number[];
    let prevMonthDays: number[];
    let nextMonthDays: number[];

    $: {
        const firstDay = getFirstDayOfMonth(year, month);
        const lastDay = getLastDayOfMonth(year, month);
        firstDayOfWeek = getDayOfWeek(firstDay);
        daysInMonth = Array.from({ length: lastDay.getDate() }, (_, i) => i + 1);

        const [prevYear, prevMonth] = getPreviousMonth(year, month);
        const lastDayOfPrevMonth = getLastDayOfMonth(prevYear, prevMonth).getDate();
        prevMonthDays = Array.from({ length: firstDayOfWeek }, (_, i) => lastDayOfPrevMonth - firstDayOfWeek + i + 1);

        const nextDaysCount = (7 - (daysInMonth.length + firstDayOfWeek) % 7) % 7;
        nextMonthDays = Array.from({ length: nextDaysCount }, (_, i) => i + 1);
    }
</script>

<style>
    .calendar {
        display: grid;
        grid-template-columns: repeat(7, 1fr);
    }
    .day {
        border: 1px solid #ccc;
        padding: 10px;
        text-align: center;
    }
    .day-name {
        font-weight: bold;
    }
    .controls {
        display: flex;
        justify-content: space-between;
        margin-bottom: 1em;
    }
    .prev-month, .next-month {
        color: #999;
    }
    .highlight {
        border-bottom: 2px solid red;
    }
</style>

<div>
    <div class="controls">
        <button on:click={previousMonth}>이전</button>
        <h1>{year}년 {month + 1}월</h1>
        <button on:click={nextMonth}>다음</button>
    </div>
    <div class="calendar">
        {#each dayNames as dayName}
            <div class="day day-name">{dayName}</div>
        {/each}

        {#each prevMonthDays as day}
            <div class="day prev-month">{day}</div>
        {/each}

        {#each daysInMonth as day}
            <div class="day {day >= 8 && day <= 12 ? 'highlight' : ''}">{day}</div>
        {/each}

        {#each nextMonthDays as day}
            <div class="day next-month">{day}</div>
        {/each}
    </div>
</div>
