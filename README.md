# iCalendar Recurrence Rule (RRULE) Sequencer

Using PHP, turn an array of iCalendar Recurrence Rule properties into an array of event sequences.

## Disclaimer

This project is new. Structural & architectural changes may occur without warning.

This disclaimer will be removed when stability has been achieved and 1.0 has been tagged.

## What is iCalendar?

iCalendar is a widely popular specification that standardizes what an “Event” is and provides directives on how software should expect to interact with them.

It is what Outlook/Office, and Apple Calendar, and Google and others all use for their calendar applications. It’s also what many WordPress Events plugins use, in one way or another, to allow for visitors to subscribe to a feed of events directly from their sites.

## What is a Recurrence Rule (RRULE)?

From: https://datatracker.ietf.org/doc/html/rfc5545#section-3.8.5.3

> This property defines a rule or repeating pattern for recurring events, to-dos, journal entries, or time zone definitions.

It is a single part of the iCalendar specification that is responsible for defining how a single event entry should reoccur multiple times into the future.

## What does this class do?

This PHP class is a generic way, without any dependencies, to parse an iCalendar `RRULE` property into an array of event sequences.

It accepts all notations of RFC5545 and its amendments (with basic error correction), looping through them to generate a sequence of events.

It has built-in support for WordPress, but (currently) does not consider it a requirement.

## Why did you build this?

We built this for recurring events in [https://sugarcalendar.com Sugar Calendar], to be able to reliably support every possible recurring event pattern.

We did a bunch of research looking for anything like this already that we could use. We found inspiration in a few places, but nothing quite as simple or effective as we were able to achieve here.

## How's it work?

In a WordPress plugin, you can test this quickly with something like...

```php
add_action( 'init', function() {

	/**
	 * First, the "INTERVAL=2" would be applied to "FREQ=YEARLY" to
     * arrive at "every other year".  Then, "BYMONTH=1" would be applied
     * to arrive at "every January, every other year".  Then, "BYDAY=SU"
     * would be applied to arrive at "every Sunday in January, every
     * other year".  Then, "BYHOUR=8,9" would be applied to arrive at
     * "every Sunday in January at 8 AM and 9 AM, every other year".
     * Then, "BYMINUTE=30" would be applied to arrive at "every Sunday in
     * January at 8:30 AM and 9:30 AM, every other year".  Then, lacking
     * information from "RRULE", the second is derived from "DTSTART", to
     * end up in "every Sunday in January at 8:30:00 AM and 9:30:00 AM,
     * every other year".  Similarly, if the BYMINUTE, BYHOUR, BYDAY,
     * BYMONTHDAY, or BYMONTH rule part were missing, the appropriate
     * minute, hour, day, or month would have been retrieved from the
     * "DTSTART" property.
	 */
	$rule = array(
		'dtstart'  => '2021-01-03 08:30:00',
		'dtend'    => '2021-01-03 09:30:00',
		'format'   => 'Y:m:d H:i:s',
		'freq'     => 'yearly',
		'interval' => '2',
		'until'    => '2030-01-01 00:00:00',
		'bymonth'  => '1',
		'byday'    => 'SU',
		'byhour'   => '8,9',
		'byminute' => '30'
	);

	// Default return value
	$retval = array();

	// Load it
	$recur = new \Sugar_Calendar\Utilities\iCalendar\Recur\Sequence( $rule );

	// Clone Events into sequences
	while ( $date = $recur->next() ) {

		// Add to return value
		$retval[] = $date;
	}

	// Dump the results
	var_dump( $retval ); die;
} );
```

The output looks something like...

```php
array (size=46)
  0 =>
    array (size=3)
      'dtstart' => string '2021-01-03 08:30:00' (length=19)
      'dtend' => string '2021-01-03 09:30:00' (length=19)
      'recurrence-id' => string '20210103T083000Z' (length=16)
  1 =>
    array (size=4)
      'dtstart' => string '2021-01-03 09:30:00' (length=19)
      'dtend' => string '2021-01-03 10:30:00' (length=19)
      'recurrence-id' => string '20210103T093000Z' (length=16)
      'sequence' => int 1
  2 =>
    array (size=4)
      'dtstart' => string '2021-01-10 08:30:00' (length=19)
      'dtend' => string '2021-01-10 09:30:00' (length=19)
      'recurrence-id' => string '20210110T083000Z' (length=16)
      'sequence' => int 2
  3 =>
    array (size=4)
      'dtstart' => string '2021-01-10 09:30:00' (length=19)
      'dtend' => string '2021-01-10 10:30:00' (length=19)
      'recurrence-id' => string '20210110T093000Z' (length=16)
      'sequence' => int 3
  4 =>
    array (size=4)
      'dtstart' => string '2021-01-17 08:30:00' (length=19)
      'dtend' => string '2021-01-17 09:30:00' (length=19)
      'recurrence-id' => string '20210117T083000Z' (length=16)
      'sequence' => int 4
  5 =>
    array (size=4)
      'dtstart' => string '2021-01-17 09:30:00' (length=19)
      'dtend' => string '2021-01-17 10:30:00' (length=19)
      'recurrence-id' => string '20210117T093000Z' (length=16)
      'sequence' => int 5
  6 =>
    array (size=4)
      'dtstart' => string '2021-01-24 08:30:00' (length=19)
      'dtend' => string '2021-01-24 09:30:00' (length=19)
      'recurrence-id' => string '20210124T083000Z' (length=16)
      'sequence' => int 6
  7 =>
    array (size=4)
      'dtstart' => string '2021-01-24 09:30:00' (length=19)
      'dtend' => string '2021-01-24 10:30:00' (length=19)
      'recurrence-id' => string '20210124T093000Z' (length=16)
      'sequence' => int 7
  8 =>
    array (size=4)
      'dtstart' => string '2021-01-31 08:30:00' (length=19)
      'dtend' => string '2021-01-31 09:30:00' (length=19)
      'recurrence-id' => string '20210131T083000Z' (length=16)
      'sequence' => int 8
  9 =>
    array (size=4)
      'dtstart' => string '2021-01-31 09:30:00' (length=19)
      'dtend' => string '2021-01-31 10:30:00' (length=19)
      'recurrence-id' => string '20210131T093000Z' (length=16)
      'sequence' => int 9
  10 =>
    array (size=4)
      'dtstart' => string '2023-01-01 08:30:00' (length=19)
      'dtend' => string '2023-01-01 09:30:00' (length=19)
      'recurrence-id' => string '20230101T083000Z' (length=16)
      'sequence' => int 10
  11 =>
    array (size=4)
      'dtstart' => string '2023-01-01 09:30:00' (length=19)
      'dtend' => string '2023-01-01 10:30:00' (length=19)
      'recurrence-id' => string '20230101T093000Z' (length=16)
      'sequence' => int 11
  12 =>
    array (size=4)
      'dtstart' => string '2023-01-08 08:30:00' (length=19)
      'dtend' => string '2023-01-08 09:30:00' (length=19)
      'recurrence-id' => string '20230108T083000Z' (length=16)
      'sequence' => int 12
  13 =>
    array (size=4)
      'dtstart' => string '2023-01-08 09:30:00' (length=19)
      'dtend' => string '2023-01-08 10:30:00' (length=19)
      'recurrence-id' => string '20230108T093000Z' (length=16)
      'sequence' => int 13
  14 =>
    array (size=4)
      'dtstart' => string '2023-01-15 08:30:00' (length=19)
      'dtend' => string '2023-01-15 09:30:00' (length=19)
      'recurrence-id' => string '20230115T083000Z' (length=16)
      'sequence' => int 14
  15 =>
    array (size=4)
      'dtstart' => string '2023-01-15 09:30:00' (length=19)
      'dtend' => string '2023-01-15 10:30:00' (length=19)
      'recurrence-id' => string '20230115T093000Z' (length=16)
      'sequence' => int 15
  16 =>
    array (size=4)
      'dtstart' => string '2023-01-22 08:30:00' (length=19)
      'dtend' => string '2023-01-22 09:30:00' (length=19)
      'recurrence-id' => string '20230122T083000Z' (length=16)
      'sequence' => int 16
  17 =>
    array (size=4)
      'dtstart' => string '2023-01-22 09:30:00' (length=19)
      'dtend' => string '2023-01-22 10:30:00' (length=19)
      'recurrence-id' => string '20230122T093000Z' (length=16)
      'sequence' => int 17
  18 =>
    array (size=4)
      'dtstart' => string '2023-01-29 08:30:00' (length=19)
      'dtend' => string '2023-01-29 09:30:00' (length=19)
      'recurrence-id' => string '20230129T083000Z' (length=16)
      'sequence' => int 18
  19 =>
    array (size=4)
      'dtstart' => string '2023-01-29 09:30:00' (length=19)
      'dtend' => string '2023-01-29 10:30:00' (length=19)
      'recurrence-id' => string '20230129T093000Z' (length=16)
      'sequence' => int 19
  20 =>
    array (size=4)
      'dtstart' => string '2025-01-05 08:30:00' (length=19)
      'dtend' => string '2025-01-05 09:30:00' (length=19)
      'recurrence-id' => string '20250105T083000Z' (length=16)
      'sequence' => int 20
  21 =>
    array (size=4)
      'dtstart' => string '2025-01-05 09:30:00' (length=19)
      'dtend' => string '2025-01-05 10:30:00' (length=19)
      'recurrence-id' => string '20250105T093000Z' (length=16)
      'sequence' => int 21
  22 =>
    array (size=4)
      'dtstart' => string '2025-01-12 08:30:00' (length=19)
      'dtend' => string '2025-01-12 09:30:00' (length=19)
      'recurrence-id' => string '20250112T083000Z' (length=16)
      'sequence' => int 22
  23 =>
    array (size=4)
      'dtstart' => string '2025-01-12 09:30:00' (length=19)
      'dtend' => string '2025-01-12 10:30:00' (length=19)
      'recurrence-id' => string '20250112T093000Z' (length=16)
      'sequence' => int 23
  24 =>
    array (size=4)
      'dtstart' => string '2025-01-19 08:30:00' (length=19)
      'dtend' => string '2025-01-19 09:30:00' (length=19)
      'recurrence-id' => string '20250119T083000Z' (length=16)
      'sequence' => int 24
  25 =>
    array (size=4)
      'dtstart' => string '2025-01-19 09:30:00' (length=19)
      'dtend' => string '2025-01-19 10:30:00' (length=19)
      'recurrence-id' => string '20250119T093000Z' (length=16)
      'sequence' => int 25
  26 =>
    array (size=4)
      'dtstart' => string '2025-01-26 08:30:00' (length=19)
      'dtend' => string '2025-01-26 09:30:00' (length=19)
      'recurrence-id' => string '20250126T083000Z' (length=16)
      'sequence' => int 26
  27 =>
    array (size=4)
      'dtstart' => string '2025-01-26 09:30:00' (length=19)
      'dtend' => string '2025-01-26 10:30:00' (length=19)
      'recurrence-id' => string '20250126T093000Z' (length=16)
      'sequence' => int 27
  28 =>
    array (size=4)
      'dtstart' => string '2027-01-03 08:30:00' (length=19)
      'dtend' => string '2027-01-03 09:30:00' (length=19)
      'recurrence-id' => string '20270103T083000Z' (length=16)
      'sequence' => int 28
  29 =>
    array (size=4)
      'dtstart' => string '2027-01-03 09:30:00' (length=19)
      'dtend' => string '2027-01-03 10:30:00' (length=19)
      'recurrence-id' => string '20270103T093000Z' (length=16)
      'sequence' => int 29
  30 =>
    array (size=4)
      'dtstart' => string '2027-01-10 08:30:00' (length=19)
      'dtend' => string '2027-01-10 09:30:00' (length=19)
      'recurrence-id' => string '20270110T083000Z' (length=16)
      'sequence' => int 30
  31 =>
    array (size=4)
      'dtstart' => string '2027-01-10 09:30:00' (length=19)
      'dtend' => string '2027-01-10 10:30:00' (length=19)
      'recurrence-id' => string '20270110T093000Z' (length=16)
      'sequence' => int 31
  32 =>
    array (size=4)
      'dtstart' => string '2027-01-17 08:30:00' (length=19)
      'dtend' => string '2027-01-17 09:30:00' (length=19)
      'recurrence-id' => string '20270117T083000Z' (length=16)
      'sequence' => int 32
  33 =>
    array (size=4)
      'dtstart' => string '2027-01-17 09:30:00' (length=19)
      'dtend' => string '2027-01-17 10:30:00' (length=19)
      'recurrence-id' => string '20270117T093000Z' (length=16)
      'sequence' => int 33
  34 =>
    array (size=4)
      'dtstart' => string '2027-01-24 08:30:00' (length=19)
      'dtend' => string '2027-01-24 09:30:00' (length=19)
      'recurrence-id' => string '20270124T083000Z' (length=16)
      'sequence' => int 34
  35 =>
    array (size=4)
      'dtstart' => string '2027-01-24 09:30:00' (length=19)
      'dtend' => string '2027-01-24 10:30:00' (length=19)
      'recurrence-id' => string '20270124T093000Z' (length=16)
      'sequence' => int 35
  36 =>
    array (size=4)
      'dtstart' => string '2027-01-31 08:30:00' (length=19)
      'dtend' => string '2027-01-31 09:30:00' (length=19)
      'recurrence-id' => string '20270131T083000Z' (length=16)
      'sequence' => int 36
  37 =>
    array (size=4)
      'dtstart' => string '2027-01-31 09:30:00' (length=19)
      'dtend' => string '2027-01-31 10:30:00' (length=19)
      'recurrence-id' => string '20270131T093000Z' (length=16)
      'sequence' => int 37
  38 =>
    array (size=4)
      'dtstart' => string '2029-01-07 08:30:00' (length=19)
      'dtend' => string '2029-01-07 09:30:00' (length=19)
      'recurrence-id' => string '20290107T083000Z' (length=16)
      'sequence' => int 38
  39 =>
    array (size=4)
      'dtstart' => string '2029-01-07 09:30:00' (length=19)
      'dtend' => string '2029-01-07 10:30:00' (length=19)
      'recurrence-id' => string '20290107T093000Z' (length=16)
      'sequence' => int 39
  40 =>
    array (size=4)
      'dtstart' => string '2029-01-14 08:30:00' (length=19)
      'dtend' => string '2029-01-14 09:30:00' (length=19)
      'recurrence-id' => string '20290114T083000Z' (length=16)
      'sequence' => int 40
  41 =>
    array (size=4)
      'dtstart' => string '2029-01-14 09:30:00' (length=19)
      'dtend' => string '2029-01-14 10:30:00' (length=19)
      'recurrence-id' => string '20290114T093000Z' (length=16)
      'sequence' => int 41
  42 =>
    array (size=4)
      'dtstart' => string '2029-01-21 08:30:00' (length=19)
      'dtend' => string '2029-01-21 09:30:00' (length=19)
      'recurrence-id' => string '20290121T083000Z' (length=16)
      'sequence' => int 42
  43 =>
    array (size=4)
      'dtstart' => string '2029-01-21 09:30:00' (length=19)
      'dtend' => string '2029-01-21 10:30:00' (length=19)
      'recurrence-id' => string '20290121T093000Z' (length=16)
      'sequence' => int 43
  44 =>
    array (size=4)
      'dtstart' => string '2029-01-28 08:30:00' (length=19)
      'dtend' => string '2029-01-28 09:30:00' (length=19)
      'recurrence-id' => string '20290128T083000Z' (length=16)
      'sequence' => int 44
  45 =>
    array (size=4)
      'dtstart' => string '2029-01-28 09:30:00' (length=19)
      'dtend' => string '2029-01-28 10:30:00' (length=19)
      'recurrence-id' => string '20290128T093000Z' (length=16)
      'sequence' => int 45
```

Results are returned as an array of `dtstart`, `dtend`, `recurrence-id`, and `sequence` parameters:

* Numbers are integers
* Date/Times are `Y-m-d H:i:s` by default
* Recurrence ID is based on the `dtstart` of the original event
* Sequence starts at `1` for the first event in the recurring sequence, after the original