ics.js - HaoTian's fork
============
What is added to the original ics.js:

- Better compatibility for recurring events
- Add reminder functionality for events (valarms)
- Add support for timezones
- Add support for geo location and Apple Calendar's map integration

----------

A browser friendly .ics/.vcs file generator written entirely in JavaScript!

Now you can make calendar friendly files client-side.  It outputs .ics files, so the files are compatible with all modern calendar software (Outlook, Apple Calendar, Google, etc.)

How To Use
----------
Simply invoke the object and use the functions...

	var cal = ics(uidDomain, prodId, vtimezone);
	cal.addEvent(subject, description, location, begin, end, rrule, alarmBefore, geo, appleGeo);
	cal.addEvent(subject, description, location, begin, end); // yes, you can have multiple events :-)
	cal.download(filename);

All `addEvent` parameters after `stop` are optional:

- `rrule` : Recurrence rule object (see [Recurring Events](#recurring-events) below)
- `alarmBefore` : Number of minutes before the event to trigger a reminder notification
- `geo` : Object with `lat`, `lon`, and optional `radius` (in metres) for a `GEO` field
- `appleGeo` : Object with `lat` and `lon` to override the Apple Maps pin location (falls back to `geo` if omitted)

`begin` and `end` need to be formatted in a way that is friendly to `Date()`

`uidDomain` sets the domain part of each event's UID. `prodId` is the calendar product name. `vtimezone` is an optional VTIMEZONE block string; when provided, `DTSTART`/`DTEND` are emitted with a `TZID` parameter instead of floating time.

Recurring Events
----------------
Recurring events can be added with the `rrule` object.

`cal.addEvent(subject, description, location, begin, end, rrule)`

The `rrule` object has the following properties:

- `freq` : __Required.__ The frequency of event recurrence. Can be `DAILY`, `WEEKLY`, `MONTHLY`, or `YEARLY`.
- `until` : A date string representing the date on which to end repitition.  Must be friendly to `Date()`
- `count` : Alternative to until.  Repeat the event `count` times.  Must be an integer
- `interval` : The interval of `freq` to recur at.  For example, if `freq` is `WEEKLY` and `interval` is `2`, the event will repeat every 2 weeks.  Must be an integer.
- `byday` : Which days of the week the event is to occur. An array containing any of `SU`, `MO`, `TU`, `WE`, `TH`, `FR`, `SA`.

The four properties described above are not exhaustive of recurrence rule capabilities.  If extra functionality is required, you can set the `rrule.rule` property to a full recurrence rule string.  In this case, none of the four properties described above are necessary.  See [this page](http://www.kanzaki.com/docs/ical/rrule.html) for a description of recurrence rules.


Example
-------
* **[Demo](https://rawgit.com/nwcell/ics.js/master/demo/demo.html)**

```html
<script>
  // Basic usage (no timezone)
  var cal = ics();
  cal.addEvent('All Day Event', 'This is an all day event', 'Nome, AK', '8/7/2026', '8/7/2026');
  cal.addEvent('Thirty Minute Event', 'This is a thirty minute event', 'Nome, AK', '8/7/2026 5:30 pm', '8/7/2026 6:00 pm');

  // With timezone
  var SEP = (navigator.appVersion.indexOf('Win') !== -1) ? '\r\n' : '\n';
  var vtimezone = [
    'X-WR-TIMEZONE:America/New_York',
    'BEGIN:VTIMEZONE',
    'TZID:America/New_York',
    'BEGIN:STANDARD',
    'DTSTART:19701101T020000',
    'TZOFFSETFROM:-0400',
    'TZOFFSETTO:-0500',
    'TZNAME:EST',
    'END:STANDARD',
    'END:VTIMEZONE'
  ].join(SEP);

  var tzCal = ics('myapp.example', 'My Calendar', vtimezone);

  // Recurring weekly event with a 15-minute reminder
  tzCal.addEvent(
    'Stand-up',
    'Daily stand-up meeting',
    'Office',
    '2026-03-02 09:00',
    '2026-03-02 09:30',
    { freq: 'WEEKLY', byday: ['MO', 'TU', 'WE', 'TH', 'FR'], until: '2026-12-31' },
    15
  );

  // Event with geo coordinates and Apple Maps integration
  tzCal.addEvent(
    'Conference',
    'Annual developer conference',
    'Moscone Center, San Francisco, CA',
    '2026-06-10 09:00',
    '2026-06-10 18:00',
    null,
    null,
    { lat: 37.7844, lon: -122.4000, radius: 100 },
    { lat: 37.7844, lon: -122.4000 }
  );

  tzCal.download('my-calendar');
</script>
<a href="javascript:tzCal.download('my-calendar')">Download Calendar</a>
```


Dependencies
------------
The tool uses 2 libraries from the following projects:
* [FileSaver.js](https://github.com/eligrey/FileSaver.js)
* [Blob.js](https://github.com/eligrey/Blob.js)

I've compressed them and included them into the source for the normal file.  Other variations are available in the repo.

If you want IE to allow for either opening documents as well as saving documents, you can use my fork of FileSaver.js (https://github.com/nwcell/FileSaver.js)...  Though you honestly are probebly best off using their main.

Supported Browsers
------------------

| Browser        | Dependancies |
| -------------- | ------------ |
| Firefox 20+    | [FileSaver.js](https://github.com/eligrey/FileSaver.js) |
| Firefox ≤ 19   | [FileSaver.js](https://github.com/eligrey/FileSaver.js), [Blob.js](https://github.com/eligrey/Blob.js) |
| Chrome         | [FileSaver.js](https://github.com/eligrey/FileSaver.js) |
| Chrome for Android v28+ | [FileSaver.js](https://github.com/eligrey/FileSaver.js) |
| IE 10+         | [FileSaver.js](https://github.com/eligrey/FileSaver.js)         |
| Opera Next     | [FileSaver.js](https://github.com/eligrey/FileSaver.js) |
| Opera < 15     | [FileSaver.js](https://github.com/eligrey/FileSaver.js), [Blob.js](https://github.com/eligrey/Blob.js) |
| Safari ≤ 6     | [FileSaver.js](https://github.com/eligrey/FileSaver.js), [Blob.js](https://github.com/eligrey/Blob.js) |


Credits
------------------
* [Travis Krause](https://github.com/nwcell): Me
* [Kyle Hornberg](https://github.com/khornberg): Added multi event functionality and made everything a package firendly
