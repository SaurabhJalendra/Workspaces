---
name: migrate-safe
description: Database migration safety wrapper — pre-mortem + backup verify + reversibility check + lock-time estimation + dry-run. MANDATORY before any schema change touching data. Prevents the most common class of irreversible data disasters.
allowed-tools: [Bash, Read, Edit, Write, Grep, Glob, Skill]
---

# /migrate-safe — Database Migration with Guardrails

Schema changes are the highest-blast-radius "small" changes. A single bad migration can lose data permanently. This skill enforces 6 gates before applying.

## When to invoke

**Auto-fire (Tier 2):**
- New migration file created in `migrations/`, `db/migrate/`, `prisma/migrations/`, etc.
- ALTER TABLE / DROP / ADD COLUMN detected in commit
- Schema definition file modified (`schema.sql`, `schema.prisma`, models.py with field changes)

**MANDATORY user-invoke before applying:**
- `/migrate-safe [migration-name]` — runs all 6 gates

## The 6 Gates

### Gate 1 — Pre-mortem
Auto-call `/premortem` skill scoped to this migration. Specifically enumerate:
- What if migration partially completes and crashes?
- What if it locks production for too long?
- What if rollback is needed mid-execution?
- What if data is lost?

If any of these can't be answered, STOP — incomplete planning.

### Gate 2 — Backup verification
```bash
# Confirm recent backup exists AND has been tested
# Postgres
pg_dump $DATABASE_URL > backup-pre-migration-$(date +%Y%m%d-%H%M%S).sql
ls -lh backup-pre-migration-*.sql  # confirm size > 0

# MySQL
mysqldump -u user -p db > backup-pre-migration-$(date +%Y%m%d-%H%M%S).sql

# SQLite
cp app.db app.db.backup-pre-migration-$(date +%Y%m%d-%H%M%S)

# MongoDB
mongodump --uri $MONGO_URI --out backup-pre-migration-$(date +%Y%m%d-%H%M%S)/
```

**Then verify the backup actually restores:**
```bash
# Spin up empty test DB, restore into it, run a sanity query
# (Procedure varies by DB. Don't skip this — untested backups have a bad track record.)
```

If backup verification fails, STOP. Data integrity > migration speed.

### Gate 3 — Reversibility check
For each migration step, write the rollback step. Common patterns:

| Forward | Rollback |
|---|---|
| `ADD COLUMN x` | `DROP COLUMN x` |
| `DROP COLUMN x` | **STOP** — data loss is permanent. Require explicit confirmation. |
| `ADD INDEX` | `DROP INDEX` |
| `RENAME COLUMN x TO y` | `RENAME COLUMN y TO x` |
| `ALTER TYPE` (lossy) | **STOP** — may not be reversible. Require explicit plan. |
| `DELETE / UPDATE rows` | Save affected rows BEFORE the operation |
| `DROP TABLE` | **STOP** — data loss permanent. Require explicit confirmation + extended retention of backup. |

If any step is non-reversible, surface it and require explicit user approval.

### Gate 4 — Lock-time estimation
For databases with table-level or row-level locks during migration:

- Estimate row count of affected tables: `SELECT COUNT(*) FROM affected_table`
- Time a similar operation on dev/staging if possible
- For Postgres: `EXPLAIN` the migration if it includes data movement
- For >100K rows OR estimated >5s lock: STOP and require migration strategy:
  - Online schema change tools (gh-ost, pt-online-schema-change for MySQL)
  - Postgres: `CREATE INDEX CONCURRENTLY`, additive-only changes, expand-then-contract pattern
  - Application-level: dual-write during transition

### Gate 5 — Dry-run
Run migration on a copy of production data (sanitized if needed):

```bash
# Postgres example
createdb migration_test
psql migration_test < backup-pre-migration-*.sql
psql migration_test -f migrations/[name].sql
# Then run application tests against migration_test
```

If dry-run fails or produces unexpected state, STOP.

### Gate 6 — Application compatibility check
- Will current application code still work after the migration runs?
- For zero-downtime: BOTH old and new app code must work with intermediate schema state
- Use expand-then-contract pattern:
  1. Migrate forward (additive only — add new column, keep old)
  2. Deploy app reading both
  3. Backfill data
  4. Deploy app reading only new
  5. Migrate forward (drop old column) — separate migration

## Output Format

```markdown
# Migration Safety Report — YYYY-MM-DD HH:MM
**Migration:** [name]
**File:** path/to/migration

## Gate 1: Pre-mortem
[Pre-mortem findings; failure modes considered]

## Gate 2: Backup
- Backup file: path
- Size: X MB
- Restore-tested: yes/no
- Status: ✅ / ❌

## Gate 3: Reversibility
| Step | Forward | Rollback | Status |
|---|---|---|---|
| 1 | ADD COLUMN x | DROP COLUMN x | ✅ reversible |
| 2 | DROP COLUMN y | (data loss) | ❌ requires confirm |

## Gate 4: Lock-time
- Affected rows: N
- Estimated duration: X seconds
- Strategy required: yes / no (status quo)

## Gate 5: Dry-run
- Test DB: name
- Result: pass / fail
- Tests run: N pass / M fail

## Gate 6: App compatibility
- Old app + new schema: works / breaks
- Strategy: blue-green / expand-contract / accepted downtime

## Decision
- [ ] All gates passed → proceed
- [ ] Some gates failed → list specific gaps; do NOT proceed
- [ ] Override requested → require explicit user typed approval

## Migration Plan
[Step-by-step execution plan with rollback at each step]
```

## Rules

- **Backup BEFORE every migration, not just "important" ones.** The "small" ones cause most accidents.
- **Dry-run MANDATORY for production migrations.** Skip only on dev DBs.
- **Expand-then-contract for zero-downtime.** Never both add new + drop old in same migration.
- **Update `wiki/log.md`** — `## [YYYY-MM-DD] migration | [name] | [outcome]`
- **Postmortem mandatory if migration goes wrong.** Even a near-miss writes a `/postmortem`.

## Anti-Patterns

- **`UPDATE` without `WHERE`** — at least once per career, every dev does this. Practice writing the SELECT first to verify scope.
- **DROP TABLE in same migration as ADD TABLE** — separate them.
- **Migration that takes 5 minutes on dev "should be fine in prod"** — prod has 100x the rows.
- **No rollback plan because "we'll fix forward"** — fix-forward in production fails 30% of the time.
- **Skipping backup because "we have replicas"** — replicas replicate the bad migration too. Take a snapshot.

## Integration

- Detect migration files via path patterns: `migrations/`, `db/migrate/`, `prisma/migrations/`, `flyway/`, etc.
- Auto-fire when commit touches these paths
- Block `/go` from proceeding if pending migration hasn't run `/migrate-safe`
- For destructive ops (DROP, irreversible ALTER), require Tier 4 confirmation pattern (Claude pauses)
