<script lang="ts">
    import { ICalendar, GoogleCalendar, OutlookCalendar } from 'datebook/dist/datebook.min.mjs';
    import pkg from 'file-saver';
    const { saveAs } = pkg;

    export let frontmatter = {};
    export let add_class = 'btn-outline-success';

    let event_location = '';
    if (typeof frontmatter.locationURL === 'string') {
        event_location = frontmatter.locationURL;
    } else if (frontmatter.locationURL) {
        event_location = frontmatter.locationURL.join(', ');
    }
    const calendar_event = {
        title: frontmatter.title,
        description: frontmatter.subtitle,
        start: frontmatter.start,
        end: frontmatter.end,
        location: event_location,
    };

    if (calendar_event.start === undefined) {
        calendar_event.start = new Date(calendar_event.startDate + 'T' + calendar_event.startTime);
    }
    if (calendar_event.end === undefined) {
        calendar_event.end = new Date(calendar_event.endDate + 'T' + calendar_event.endTime);
    }

    const googleCalendar = new GoogleCalendar(calendar_event).render();
    const outlookCalendar = new OutlookCalendar(calendar_event).render();
    let ical = new ICalendar(calendar_event).render();
    const blob = new Blob([ical], { type: 'text/calendar;charset=utf-8' });
    function downloadIcal() {
        saveAs(blob, frontmatter.title.replace(/[^a-z0-9]/gi, '_').toLowerCase() + '.ics');
    }
</script>

<div class="dropdown btn-group position-relative" role="group">
    <button
        type="button"
        class={'btn dropdown-toggle ' + add_class}
        href="#"
        data-bs-toggle="dropdown"
        aria-haspopup="true"
        aria-expanded="false"
    >
        <i class="far fa-calendar-plus me-1" aria-hidden="true" /> Export event
    </button>
    <div class="dropdown-menu">
        <button class="dropdown-item" on:click={() => downloadIcal()}> Download iCal Event</button>
        <a class="dropdown-item" href={googleCalendar} target="_blank" rel="noreferrer"> Add to Google Calendar</a>
        <a class="dropdown-item" href={outlookCalendar} target="_blank" rel="noreferrer"> Add to Microsoft Outlook</a>
    </div>
</div>
