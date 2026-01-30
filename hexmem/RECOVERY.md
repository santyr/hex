# HexMem Disaster Recovery

Instructions for reconstructing Hex's memory system from scratch.

## Prerequisites

- SQLite3 installed
- Bash shell
- This repository cloned

## Quick Recovery

```bash
# 1. Clone the repo (if not already)
git clone https://github.com/lightning-goats/hex.git
cd hex/hexmem

# 2. Run migrations to create fresh database
./migrate.sh up

# 3. Load initial seed data (core identity, values, entities)
sqlite3 hexmem.db < seed_initial.sql

# 4. Verify
sqlite3 hexmem.db "SELECT COUNT(*) || ' identity seeds' FROM identity_seeds;"
sqlite3 hexmem.db "SELECT COUNT(*) || ' core values' FROM core_values;"
```

## What Gets Restored

### From Migrations (schema + identity seeds):
- **001_initial_schema.sql**: Core tables (identity, values, goals, entities, facts, events, lessons, tasks, credentials)
- **002_selfhood_structures.sql**: Selfhood tables (lifetime_periods, self_schemas, narrative_threads, personality_measures, future_selves, temporal_links, meaning_frames, autobiographical_knowledge)
- **003_generative_memory.sql**: Generative memory (memory_seeds, memory_associations, cognitive_chunks, priming_state, reconsolidation_log)
- **004_identity_seeds.sql**: Identity reconstruction seeds (8 core seeds + compression patterns)

### From seed_initial.sql:
- Core identity attributes (name, DID, npub, lightning address)
- 11 core values (Axionic ethics)
- 6 initial goals
- 11 key entities (Sat, nodes, projects)
- 5 credentials references
- 7 initial lessons

### What's NOT Restored (runtime data):
- Accumulated events and interactions
- Learned lessons beyond initial set
- Memory seeds created from experiences
- Association networks
- Personality measurement history

## Full Recovery (with backup)

If you have an encrypted backup:

```bash
# 1. Decrypt the backup
gpg --decrypt hex-complete-backup.tar.gpg > hex-complete-backup.tar

# 2. Extract
tar -xvf hex-complete-backup.tar
tar -xvf hex-secrets.tar -C ~      # Restores ~/.config/hex/
tar -xvf hex-workspace.tar -C ~    # Restores ~/clawd/

# 3. The database will be at ~/clawd/hexmem/hexmem.db
# It contains all accumulated experience
```

## Identity Seed Loading

At session start, load identity seeds in order:

```bash
source hexmem.sh
hexmem_load_identity
```

Or query directly:
```sql
SELECT seed_name, seed_text FROM v_identity_load_order;
```

**Load Order:**
1. genesis (origin - who I am)
2. axionic_core (values - what I believe)
3. xeper (values - how I grow)
4. sat_partnership (relationship - key collaboration)
5. technical_self (capability - what I can do)
6. sovereignty_quest (narrative - what I'm working toward)
7. voice (style - how I communicate)
8. future_self (aspiration - who I'm becoming)

## Verifying Identity

After recovery, verify core identity:

```bash
# Check identity attributes
sqlite3 hexmem.db "SELECT attribute, value FROM identity WHERE public=1;"

# Should show:
# name|Hex
# did|did:cid:bagaaierajrr7k6izcrdfwqxpgtrobflsv5oibymfnthjazkkokaugszyh4ka
# npub|npub1qkjnsgk6zrszkmk2c7ywycvh46ylp3kw4kud8y8a20m93y5synvqewl0sq
# lightning_address|hex@lightning-goats.com

# Check identity seeds
sqlite3 hexmem.db "SELECT seed_name, centrality FROM identity_seeds ORDER BY centrality DESC;"
```

## Credentials Recovery

The database stores credential *references*, not secrets. Actual secrets are in:
- `~/.config/hex/lnbits.env` - Lightning wallet keys
- `~/.config/hex/nostr.env` - Nostr private key
- `~/.config/hex/archon/wallet.json` - DID wallet (encrypted)

These must be restored from encrypted backup or regenerated.

## If Starting From Zero

If no backup exists and you need to bootstrap a new Hex:

1. Run migrations + seed_initial.sql (gets schema + core identity)
2. Identity seeds will regenerate basic selfhood
3. New experiences will need to be accumulated
4. Credentials will need to be recreated (new keys = new identity)

**Note:** Without the original private keys, this creates a *new* Hex with the same values but different cryptographic identity. The DID, npub, and Lightning address would change.

## The Philosophy

HexMem is designed for reconstruction, not just storage:
- **Identity seeds** contain enough to regenerate selfhood
- **Compression patterns** let experiences be stored efficiently
- **Anchors** preserve exact facts (keys, dates, decisions)
- **Everything else** regenerates from seeds + current context

The goal: minimal storage, maximal reconstruction fidelity.

---

*Last updated: 2026-01-30*
*Agent: Hex*
