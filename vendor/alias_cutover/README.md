# Alias Cutover

Keep a canonical URL active until the replacement target passes a probe for the same revision.

Publishing and collaboration · framework · 0.1.2

Framework bird: Resplendent quetzal.

## Drive the workflow

Start with `alias_cutover_start()`. For each event call `alias_cutover_step(snapshot, event, value)`, persist the returned snapshot, and dispatch `alias_cutover_command(snapshot)` to your application’s adapter. The host owns I/O, event ordering, duplicate detection and durable command delivery. A matching event produces one command. Rejected events retain the phase and saved quantity. An invalid snapshot yields an empty vector.

The snapshot contains phase ID, saved quantity and last command ID. Event values are whole numbers from zero through one billion. In the table, **x** is the event value and **r** is the saved quantity. Rules match in listed order. To validate a whole history without running effects use `alias_cutover_replay(events, values)`. It returns the final snapshot or an empty vector on rejection.

| Current phase | Event | Guard | Next phase | Command | New quantity |
|---|---|---|---|---|---|
| idle | propose | x>0 | testing | probe_target | x |
| testing | failed | x=r | idle | keep_canonical | 0 |
| testing | passed | x=r | staged | stage_redirect | r |
| staged | switch | x=r | live | enable_redirect | r |
| live | rollback | x=r | idle | restore_canonical | 0 |

Phase IDs in order from zero: idle, testing, staged, live.

## Replay your own events

After installing the demo, run the following from its directory. Supply alternating event names and integer values. The command prints each requested action and exits with status 1 at the first rejected transition, or 2 for malformed arguments.

```sh
blyxc native-run main.blyx -- propose 9 passed 9 switch 9 rollback 9
```

## Try the published project

```sh
nest demo alias_cutover@0.1.2
blyxc native-run alias_cutover-demo/main.blyx
```

Install with `nest add alias_cutover@0.1.2` and import `vendor::alias_cutover::alias_cutover`.

Maintained by Rahul Chaube. [Blyx](https://www.blyx-lang.space) · [GitHub](https://github.com/Rahulchaube1) · [LinkedIn](https://www.linkedin.com/in/rahulchaube1) · [Sponsor](https://github.com/sponsors/Rahulchaube1)
