# Variants: one check, run under each environment a gate names

Drafted clauses that let a branch's gate require a check to pass under
each of several environments the policy names, such as each release of
the task runner a repository supports, as it requires a check to pass
on each of several systems (86). A run is then made for each system
and variant the gate names, and a result belongs to its key, its system
and its variant. Part A gains clauses 118 to 121; 27, 69, 85 and 108
gain the variant; the vocabulary gains one entry; Part C gains
`[variants]` and `variants_for` in C.7 and the variant in C.2's
records and C.6's statement. Nothing here edits `../requirements.md`;
the operator rules on it.

Not adopted. No repository of the family asks for a check under several
releases of a tool today, so the design agent keeps it here for the
first that does. Its numbers are taken since it was drafted: 118 to
121 and S50 and S51 are ratified for pull requests, so adopting it
numbers its clauses afresh.

## What it answers

| question | answered by |
|---|---|
| how a gate requires `#check-fast` to pass under moon 2.6 and moon 2.7 | 118, 119: the policy names the variants; the gate names, per check, which of them a pass must come from |
| what a variant is to sluice | 118: a name and the environment a run of it is started with, nothing more |
| whether a pass under one variant stands for another | 119: never, as a pass on one system never stands for another (86) |
| how a run's variant is known on another host | 120: its record names it, and a signer signs it with the run |
| whether a replay counts for a variant | 108 as amended: only with a trusted pass for the same key, system and variant |
| what an executor that cannot run a variant does | 121: the run has no outcome when the readiness the repository declares fails under it (114) |
| what a check with no variants named does | runs once, with no variant, as today |

## The shape

```
 [variants]
 moon-2.6 = { env = { PROTO_MOON_VERSION = "2.6.4" } }
 moon-2.7 = { env = { PROTO_MOON_VERSION = "2.7.1" } }

 [branches.main.variants_for]
 "#check-fast" = ["moon-2.6", "moon-2.7"]

 candidate C on main, systems_for "#check-fast" = linux-x64, macos-arm64
   -> four runs: (linux-x64, moon-2.6) (linux-x64, moon-2.7)
                 (macos-arm64, moon-2.6) (macos-arm64, moon-2.7)    (119)
   -> each recorded under its key, system and variant              (120)
   -> the verdict reads all four; one fail fails the candidate     (15, 119)
```

## The problem

A repository that ships a plugin or a library for a tool wants its
checks to pass under each release of the tool it supports: moon 2.6
and moon 2.7, Node 20 and 22, Postgres 16 and 17. The statement lets a
gate require a check on several systems (86), and nothing else.
Everything else about the run is the repository's, fixed by its tree.

A repository can work around it by writing one task per release,
`check-fast-moon26` and `check-fast-moon27`. That doubles every task
in the repository's own configuration, and every new release edits
every task. It cannot express "the same check, under each of these",
which is what the operator means.

Nor does a key settle it. Whether a check's key changes with its
environment is the task runner's to decide (C.2): moon's hash covers
the variables a task declares as inputs, and a variable the variant
sets that no task declares leaves the key as it was. Two runs under two
releases can then share a key. If a result belonged to its key alone, a
pass under 2.7 would read as a pass under 2.6.

## What the statement already settles

A check's result belongs to its key and its system (69), and a pass on
one system never stands for another (86). A gate names the systems
each required check needs, and a candidate's verdict is read over
every check on every system named (86, 15). Runs are signed with what
the signer claims of them (79), replays count only with a trusted pass
behind them (108), and a run whose host fails the readiness the
repository declares has no outcome (114). A variant follows the same
road a system took, one level down: it is a second axis of the same
matrix, set by the policy and claimed in the record.

What differs: a system is what the machine is, so only an executor of
that system may take a run of it (87). A variant is what the run is
started with, so any executor of the right system can run any variant,
as long as its host can.

## The ruling

> 118. A branch's policy may name variants: each a name and the
>      environment a run of it is started with. A variant is nothing
>      else to sluice: what the environment selects, a release of a
>      tool, a database or a flag, is the repository's to arrange.

> 119. A branch's gate names, for each check it requires, the variants
>      a pass must come from, on each system it names for that check
>      (86). A candidate's verdict is read over every required check on
>      every system and variant named, and a pass under one variant
>      never stands for another. A check for which the gate names no
>      variant runs with none, and only a run with no variant counts
>      for it.

> 120. A run record names the variant its run was started with, or that
>      it had none, and the environment the variant gave it. The
>      variant is part of what the signer signs (79), and a result
>      belongs to its key, its system and its variant (69).

> 121. A run of a variant is started with that variant's environment,
>      the readiness the repository declares included (114). A host the
>      variant's environment leaves unready refuses the run, which then
>      has no outcome, as any unready host's does, and is taken again.

118 keeps sluice out of the tool business. It sets variables and
records them; proto, nix, asdf or a container image tag turn them into
a release of moon. A variant that names nothing the repository reads
runs the same check twice, and sluice cannot tell, as it cannot tell
whether a system's toolchain differs (79).

119 is 86 again, for the second axis. The cost is the product: three
systems and two variants are six runs of a check, and a gate names it
only for the checks where it matters, the way `systems_for` does.

120 is what lets a run on one host count on another. Since a result is
read under its variant, a replay under one variant never stands for a
run under another, and 108 reads with the variant:

> 108. A result a tool answered from an earlier run counts for a gate only
>      when a signer the gate trusts has signed a pass for the same key on
>      the same system and variant (15, 16). What the tool's cache holds
>      supplies outputs and decides nothing.

121 makes a missing release an unready host, not a fail. A host
without moon 2.6 that is asked for it refuses the run instead of
failing it; the readiness command the repository declares, run under
the variant's environment, is how it knows. The run goes to a host
that can, or stays open until the candidate's time limit (66), as a run
of a system no executor is does (87).

27 gains the variants:

> 27. A branch's gate is its policy's: the checks it requires, the
>     signers it trusts and the claims each may make, the systems and
>     variants each check must pass on, how a flaky key reads, whether
>     every candidate or the deepest first is tested, the window's
>     bounds, and how long a candidate's checks may run. sluice gives no
>     gate a meaning of its own, and admits every submission a driver
>     makes: whether work may be submitted at all is decided before it
>     reaches sluice.

69 and 85 gain the variant:

> 69. A check's result belongs to its key, its system and its variant,
>     not to a branch: a pass recorded under a key on a system and
>     variant satisfies every branch whose gate trusts the signer that
>     signed it and requires that system and variant.

> 85. A run record names the system its run executed on and the variant
>     it was started with: one check on two systems, or under two
>     variants, is two results (69).

19 reads as written: a flaky key is flaky on its system and variant,
since that is what a result belongs to.

## Vocabulary

- **variant**: a name a branch's policy gives to an environment a run
  is started with, which a gate may require a check to pass under (118).

## Part C

### C.7 Policy

`[variants]` names each variant and its environment. A branch's
`variants_for` names, per check, the variants a pass must come from;
a branch-wide `variants` applies to every required check that
`variants_for` does not name. Both are read from the branch's current
revision (56).

```toml
[variants]
moon-2.6 = { env = { PROTO_MOON_VERSION = "2.6.4" } }
moon-2.7 = { env = { PROTO_MOON_VERSION = "2.7.1" } }

[branches.main]
required = ["#check-fast", "#integration", "cli:build"]
systems = ["linux-x64"]

[branches.main.variants_for]
"#check-fast" = ["moon-2.6", "moon-2.7"]
```

A variant's environment is set over the run's own, after the place's
and before the check starts; it never sets `SLUICE_*`, which are the
place's.

### C.2 The check

Each run report and run record carries `variant`, its name or absent,
and `variant_env`, the variables it set. The moon profile's key is
moon's hash, unchanged: the variant sits beside it, never inside it.

### C.6 The records

On a signer's ref, a run record's subject is `<check>@<system>` for a
run with no variant and `<check>@<system>+<variant>` for one with; the
predicate names the variant and its environment. On the local files,
`results.jsonl` gains `variant`; a line without it is a run with none.

## For the caller

`sluice status` names each of a candidate's runs by its system and
variant, so a session told of a failure (20) reads which release failed:
`#check-fast on linux-x64 under moon-2.6`. A host that is to run
variants installs what they select, and its readiness command checks
it is there.

## Alternatives the operator may prefer

- **The variant inside the key.** The repository declares the variant's
  variables as task inputs, so each variant has its own key, and the
  statement changes nothing. It fails the problem: the gate still
  cannot require a check under each variant, only accept one; and the
  key's coverage is the task runner's (C.2), which sluice does not read.
- **A variant as a system.** `linux-x64-moon26` as a system. It needs no
  new clause, but a system is a claim of what the machine is (79, 87):
  a standing executor would have to claim every system-and-release pair
  it can run, and a signer allowed `linux-x64` could not run a variant
  without the policy listing each pair.
- **Variants as a matrix of the whole gate**, every check under every
  variant. Simpler to write, and it multiplies every check, where the
  operator usually needs one or two.

## Scenarios

- **S50.** `main`'s gate requires `#check-fast` on `linux-x64` under
  `moon-2.6` and `moon-2.7`. A candidate runs it twice, once under
  each, and both pass. The candidate passes and lands (119).
- **S51.** As S50, but the run under `moon-2.6` fails and the run under
  `moon-2.7` passes. The candidate fails, and its culprit's session is
  told `#check-fast` failed under `moon-2.6` (119, 20).
- **S52.** Both runs of S50 share one key. The task runner replays the
  second from the first's cache. The replay counts for nothing, since
  no signer has signed a pass under `moon-2.6` for that key, and the
  check runs under it (108, 120).
- **S53.** The only `linux-x64` host has no moon 2.6. Its readiness
  command fails under `moon-2.6`'s environment, and it refuses the run,
  which has no outcome. The run stays open until a host that can takes
  it, or the candidate's time limit reads it not passed (121, 66).
- **S54.** `bolt/*`'s gate names no variants. A pass on a bolt branch
  under no variant does not count for `main`'s `#check-fast` under
  `moon-2.6`, and `main` runs it (119).

## What it changes elsewhere

- 27, 69, 85 and 108 as above; one glossary entry.
- C.2: `variant` and `variant_env` on run reports and records.
- C.6: the variant in a signed statement's subject and predicate, and in
  `results.jsonl`.
- C.7: `[variants]`, `variants` and `variants_for`.
- The site: the variants in the configuration reference, and a section
  in the executors and systems guide on testing several releases.
- sluice: the plan of a candidate's runs crosses systems with variants;
  the executor sets the variant's environment over the place's and runs
  the readiness command under it; results are matched by key, system
  and variant; the signed predicate and its subject name the variant.
