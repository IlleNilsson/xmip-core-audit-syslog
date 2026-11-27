# xmip-core-audit-syslog

The `syslog` technology of `xmip-core-audit`: the operating system's log on
Unix, where a record goes when audit cannot persist it (ADR-0062 clause 3).
`Syslog` is an `audit::operating_system_log::OperatingSystemLog`; the
program that runs hands it to audit as it starts
(`operating_system_log::hand`), and audit's fallback rule writes through
it. The rule — when, and the sentence that says why — is audit's; this
crate writes the entry.

One datagram, `<PRI>xmip[pid]: text`, as the C library's `syslog(3)` sends
one, to the first local socket that takes it: `/dev/log` — the journal's
own socket on a systemd machine (`journalctl -t xmip`), a syslog daemon's
elsewhere — then `/var/run/syslog`, which macOS's unified log reads, then
`/var/run/log` on the BSDs. Facility user; error 3, warning 4,
informational 6 (RFC 5424); cut at 8 KiB, on a character boundary. No
unsafe code reaches it.

Unix only: on any other platform this crate is empty, and
`xmip-core-audit-windows-event-log` is the log there.

No test writes to the machine's log by default: the one that does is
ignored, and `cargo test -- --ignored` runs it to prove an entry reaches
the local syslog.

`architecture.toml` carries the maturity.

## Verification

The included workflow is manual-only and calls the versioned shared workflow at
`IlleNilsson/.github@v1`.
