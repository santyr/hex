# HexMem - Hex's Memory Database

A SQLite-based persistent memory system for agent continuity.

## Philosophy

This database augments (not replaces) narrative memory files. It provides:
- **Structured recall**: Query facts, events, relationships by attribute
- **Temporal memory**: Track when things happened, find patterns
- **Self-persistence**: Store identity, values, goals, lessons
- **Extensibility**: Migrations allow schema evolution over time

## Schema Overview

### Identity (Who I Am)
- `identity` - Core attributes (name, DID, npub, etc.)
- `values` - Ethical commitments and beliefs
- `goals` - What I'm working toward
- `preferences` - How I like to do things

### Knowledge (What I Know)
- `entities` - People, systems, projects, concepts
- `entity_aliases` - Multiple names for same entity
- `facts` - Subject-predicate-object triples
- `relationships` - How entities relate

### Memory (What Happened)
- `events` - Timeline of significant occurrences
- `interactions` - Conversations and exchanges
- `lessons` - Wisdom accumulated from experience
- `sessions` - Track conversation sessions

### Operations
- `tasks` - Things to do
- `credentials` - Access and capabilities
- `kv_store` - Flexible key-value storage

### Search (Future)
- `embeddings` - Vector embeddings for semantic search
- `memory_chunks` - Indexed narrative memory

## Usage

### Migrations

```bash
# Check status
./migrate.sh status

# Apply pending migrations
./migrate.sh up
```

### Direct queries

```bash
# Interactive shell
sqlite3 ~/clawd/hexmem/hexmem.db

# Query from command line
sqlite3 ~/clawd/hexmem/hexmem.db "SELECT * FROM v_active_goals;"
```

### From scripts

```bash
source ~/clawd/hexmem/hexmem.sh

# Add a fact
hexmem_add_fact "hive-nexus-01" "capacity_sats" "165000000"

# Log an event
hexmem_log_event "fleet" "Channel rebalanced" "Moved 500k sats from X to Y"

# Record a lesson
hexmem_add_lesson "lightning" "High-traffic channels need lower fees"
```

## Backup

The database is a single file. Include it in backups:
```bash
cp ~/clawd/hexmem/hexmem.db ~/hex-backup-YYYYMMDD/
```

Or it's included automatically in the full workspace backup.

## Migration Guidelines

1. Create numbered files: `NNN_description.sql`
2. Include both UP and comments
3. Never modify applied migrations
4. Test on a copy first

## Files

```
hexmem/
├── hexmem.db           # The database (created on first migrate)
├── migrate.sh          # Migration runner
├── hexmem.sh           # Shell helper functions
├── README.md           # This file
└── migrations/
    ├── 001_initial_schema.sql
    └── ...
```

---
Created: 2026-01-30
Agent: Hex
