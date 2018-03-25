# Quick plan
is a command line tool to define, import, schedule and share GarminConnect workouts i.e. weekly based training plans.

An example of a workout definition notation:
```sh
workout: 15k, 3x3.2k @HMP
- warmup: 2km @z2
- repeat: 3
  - run: 3200m @ 5:05-4:50
  - recover: 800m @z2
- run: 1km @z2
- cooldown: lap-button
```
and the tool's job is actually to translate it to this:
![15k workout](https://i.imgur.com/vxXNV7w.png)

## File format

The file format is a spreadsheet exported to csv format. The 1st row is reserved for heading, can be anything (you define your first day of a week etc.). The 1st column is reserved for a week number. The cells represent days and they are populated with workouts (definitions and references), so there's a limitation: a single workout per day/cell, for now. In case of the references, a workout needs to be defined first in one of the previous cells and then it can be referenced by name in the following cells.

An example of 2-weeks training plan, containing 2 workout definitions, 4 references and 6 training days in total:

| Week | Mon | Tue | Wed | Thu | Fri | Sat | Sun |
| ----:| --- | --- | --- | --- | --- | --- | --- |
| 1    | ``workout: run-fast``<br>``- warmup: 10:00 @ z2``<br>``- repeat: 3``<br>&nbsp;&nbsp;``- run: 1.5km @ 5:10-4:40``<br>&nbsp;&nbsp;``- recover: 500m @ z2``<br>``- cooldown: 05:00``|rest|rest|run-fast|rest|rest|rest|
| 2    | run-fast| ``workout: long-15`` <br> ``- run: 15 km @ z2``|rest|run-fast|rest|rest|long-15|

Checkout a [complete training plan for 80K ultra](https://docs.google.com/spreadsheets/d/1b1ZzrAFrjd-kvPq11zlbE2bWn2IQmUy0lBqIOFjqbwk/edit?usp=sharing). It was originally published in an article of Runner's world website - here's [the link](https://www.runnersworld.com/ultrarunning/the-ultimate-ultramarathon-training-plan).
## Command line options

```
quick-plan --help

quick-plan 0.x

Usage: quick-plan [import|schedule] [options] <file>

  -e, --email <value>      E-mail to login to Garmin Connect
  -p, --password <value>   Password to login to Garmin Connect
  -d, --delete             Delete all existing workouts with same names as the ones that are going to be imported.
  --help                   prints this usage text

  <file>                   File with a weekly based plan in CSV format


Command: import
Imports all workout definitions from CSV file. If it's omitted, it is will be on by default.
Command: schedule [options]
Schedules your weekly plan defined in CSV in Garmin Connect calendar, starting from the first day of first week or ending on the last day of the last week. Either start or end date must be entered so the scheduling can be done properly. In case both are entered, start date has priority. All dates have to be entered in ISO date format e.g. '2018-03-24'.

  -s, --start <value>      Date of the first day of the first week of the plan
  -n, --end <value>        Date of the last day of the last week of the plan

EXAMPLES

Schedules ultra 80k plan targeting 28-4-2018 for a race day

quick-plan schedule -n 2018-04-29 -d -e your-mail-address@example.com ultra-80k-runnersworld.csv
```

## Workout notation
The reserved keywords of the notation are: workout, warmup, cooldown, run, repeat, recover and lap-button.

**`workout`** := `<header> <step>+`

**`<header>`** := `workout: <name>`

**`<step>`** := `<newline> - <step-def>*`

**`<step-def>`** := `<simple-step> | <repetition-step> `

**`<repetition-step>`** := `repeat: <count> <step>+`

**`<simple-step>`** := `- (warmup | cooldown | run | recover): <duration> [@ <target>]`

**`<duration>`** := `<distance-duration> | <time-duration> | lap-button`

**`<distance-duration>`** := `<number> (km | m | mi)`

**`<time-duration>`** := `<minutes>:<seconds>`

**`<target`>** := `<zone-target> | <pace-target>`

**`<zone-target`>** := `z[1-6]`

**`<pace-target`>** := `<pace> - <pace>`

**`<pace`>** := `<minutes>:<seconds>`

**`<minutes`>** := `\d{1,2}`

**`<seconds`>** := `\d{2}`