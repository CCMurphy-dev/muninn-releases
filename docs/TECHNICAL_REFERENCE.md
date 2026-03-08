# Muninn Technical Reference

For developers, IT staff, and anyone integrating with or troubleshooting Muninn at a technical level.

---

## Database Schemas

### Tracking Database (`muninn_tracking.db`)

The central tracking database uses SQLite with WAL mode for concurrent network access. Located at `{department_root}/tracking/muninn_tracking.db`.

**Current schema version:** 10

```sql
-- Trainee registry
CREATE TABLE trainees (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT,
    level TEXT NOT NULL DEFAULT 'Unknown',
    start_date TEXT,
    supervisor TEXT,
    active INTEGER DEFAULT 1,
    pin_hash TEXT,                    -- Argon2id hash (nullable)
    created_at TEXT DEFAULT (datetime('now')),
    updated_at TEXT DEFAULT (datetime('now'))
);

-- Practice attempts (from Muninn trainee app)
CREATE TABLE practice_attempts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    trainee_id TEXT NOT NULL,
    case_id TEXT NOT NULL,
    case_path TEXT NOT NULL,
    course_name TEXT,
    course_type TEXT,
    modality TEXT,
    diagnosis TEXT,
    submitted_findings TEXT,
    submitted_diagnosis TEXT,
    submitted_differential TEXT,
    submitted_management TEXT,
    self_rating INTEGER CHECK (self_rating IS NULL OR (self_rating >= 1 AND self_rating <= 5)),
    time_taken_seconds INTEGER,
    practice_mode TEXT,               -- 'exam' or 'learning'
    submitted_at TEXT DEFAULT (datetime('now')),
    FOREIGN KEY (trainee_id) REFERENCES trainees(id)
);

-- Exam definitions
CREATE TABLE exams (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    exam_name TEXT NOT NULL UNIQUE,
    config_path TEXT,
    results_path TEXT,
    course_type TEXT,
    modality TEXT,
    case_count INTEGER DEFAULT 0,
    marking_scheme TEXT,              -- 'legacy' | 'dimension' | 'rcr_cr2b' (v9+)
    created_at TEXT DEFAULT (datetime('now'))
);

-- Exam submissions (raw trainee answers)
CREATE TABLE exam_submissions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    exam_id INTEGER NOT NULL,
    trainee_id TEXT NOT NULL,
    case_id TEXT NOT NULL,
    case_path TEXT NOT NULL,
    correct_diagnosis TEXT,
    submitted_findings TEXT,
    submitted_diagnosis TEXT,
    submitted_differential TEXT,
    submitted_management TEXT,
    self_rating INTEGER CHECK (self_rating IS NULL OR (self_rating >= 1 AND self_rating <= 5)),
    time_taken_seconds INTEGER,
    exam_type TEXT CHECK (exam_type IN ('short', 'long')),
    submitted_at TEXT DEFAULT (datetime('now')),
    submission_status TEXT NOT NULL DEFAULT 'submitted',  -- 'submitted' | 'not_submitted' (v10+)
    FOREIGN KEY (exam_id) REFERENCES exams(id),
    FOREIGN KEY (trainee_id) REFERENCES trainees(id),
    UNIQUE(exam_id, trainee_id, case_id)
);

-- Marking entries (graded by educators)
CREATE TABLE marking_entries (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    exam_id INTEGER NOT NULL,
    trainee_id TEXT NOT NULL,
    case_id TEXT NOT NULL,
    mark REAL CHECK (mark IS NULL OR (mark >= 0 AND mark <= 10)),
    feedback TEXT,
    marker_id TEXT,
    marked_at TEXT DEFAULT (datetime('now')),
    -- Dimension scoring fields (null for legacy entries)
    detection_score REAL,
    detection_max REAL,
    description_score REAL,
    description_max REAL,
    diagnosis_score REAL,
    diagnosis_max REAL,
    management_score REAL,
    management_max REAL,
    critical_miss INTEGER DEFAULT 0,
    critical_miss_reason TEXT,
    FOREIGN KEY (exam_id) REFERENCES exams(id),
    FOREIGN KEY (trainee_id) REFERENCES trainees(id),
    UNIQUE(exam_id, trainee_id, case_id)
);

-- Audit log for compliance
CREATE TABLE audit_log (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp TEXT DEFAULT (datetime('now')),
    actor TEXT NOT NULL,              -- trainee_id or 'admin'
    action TEXT NOT NULL,             -- exam_start, exam_submit, pin_failed, mark_entered, etc.
    target_type TEXT,                 -- exam, trainee, submission
    target_id TEXT,                   -- exam_name, trainee_id, case_id
    details TEXT,                     -- JSON blob
    app_version TEXT
);

-- License storage (single row)
CREATE TABLE license (
    id INTEGER PRIMARY KEY CHECK (id = 1),
    license_key TEXT NOT NULL,
    license_type TEXT NOT NULL DEFAULT 'standard',
    department_name TEXT NOT NULL,
    issued_at TEXT NOT NULL,
    expires_at TEXT,
    signature TEXT NOT NULL,
    grace_period_days INTEGER DEFAULT 30,
    activated_at TEXT DEFAULT (datetime('now'))
);

-- Backup metadata
CREATE TABLE backup_metadata (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    backup_path TEXT NOT NULL,
    backup_type TEXT NOT NULL,        -- daily, weekly, manual
    created_at TEXT DEFAULT (datetime('now')),
    size_bytes INTEGER,
    schema_version TEXT
);

-- Exam distribution tracking
CREATE TABLE exam_distributions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    exam_name TEXT NOT NULL,
    exam_config_path TEXT NOT NULL,
    trainee_id TEXT NOT NULL,
    distributed_at TEXT DEFAULT (datetime('now')),
    pin_generated INTEGER DEFAULT 0,
    FOREIGN KEY (trainee_id) REFERENCES trainees(id)
);
```

### Case Index Database (`case_index.db`)

Full-text search index built by Muninn Admin, stored at `{department_root}/library/.muninn/case_index.db`.

**Current schema version:** 8

```sql
-- FTS5 virtual table for full-text search
CREATE VIRTUAL TABLE cases_fts USING fts5(
    case_id,
    diagnosis,
    history,
    findings,
    differential,
    subspecialty,
    modality,
    author,
    content=cases,
    content_rowid=id
);

-- Case metadata
CREATE TABLE cases (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    case_id TEXT NOT NULL,
    path TEXT NOT NULL UNIQUE,
    diagnosis TEXT,
    history TEXT,
    modality TEXT,
    subspecialty TEXT,
    difficulty TEXT,
    author TEXT,
    has_notes INTEGER DEFAULT 0,
    has_key_slices INTEGER DEFAULT 0,
    series_count INTEGER DEFAULT 0,
    indexed_at TEXT DEFAULT (datetime('now')),
    file_modified_at TEXT              -- For staleness detection
);

-- Index metadata
CREATE TABLE index_metadata (
    key TEXT PRIMARY KEY,
    value TEXT
);
```

---

## File Formats

### case_data.json

Complete case metadata including DICOM series information. Written by the DICOM Organizer (series data) and Case Loader (teaching content). The Case Loader preserves existing series data when adding teaching fields.

```json
{
  "case_id": "01_Acute_Appendicitis",
  "diagnosis": "Acute appendicitis with perforation",
  "history": "45-year-old male with 2 days of RIF pain and fever",
  "modality": "CT",
  "body_region": "Abdomen",
  "subspecialty": "GI",
  "difficulty": "intermediate",
  "age": "45",
  "gender": "M",
  "author": "Dr Smith",
  "differential": ["Mesenteric adenitis", "Crohn's disease", "Ovarian pathology"],
  "created_at": "2026-02-14T10:30:00Z",
  "updated_at": "2026-02-14T14:20:00Z",
  "series": [
    {
      "uid": "1.2.840.113619.2.55.3.12345",
      "description": "Axial CT Abdomen Pelvis",
      "modality": "CT",
      "folder": "01_Axial_CT_Abdomen_Pelvis",
      "slice_count": 245,
      "window_center": 40,
      "window_width": 400,
      "rows": 512,
      "columns": 512,
      "bits_stored": 16,
      "pixel_spacing": [0.7, 0.7],
      "slice_thickness": 1.25
    }
  ],
  "scoring": {
    "scheme": "dimension",
    "detection_weight": 0.3,
    "description_weight": 0.3,
    "diagnosis_weight": 0.3,
    "management_weight": 0.1,
    "detection_max": 3,
    "description_max": 3,
    "diagnosis_max": 3,
    "management_max": 1,
    "management_required": true,
    "critical_findings": ["Appendicolith", "Periappendiceal fat stranding"]
  }
}
```

The `scoring` object is optional and used only for dimension-marked exams. `scheme` defaults to `"dimension"` if absent; set to `"rcr_cr2b"` for that scheme.

### key_slices.json

Annotated findings with slice references.

```json
{
  "findings": [
    {
      "id": "f1",
      "series": "01_Axial_CT_Abdomen_Pelvis",
      "slice": 142,
      "description": "Dilated appendix (12mm) with periappendiceal fat stranding",
      "window_center": 40,
      "window_width": 400
    }
  ]
}
```

### Playlist JSON

Stored in `library/playlists/`. The `@library/` path prefix resolves to the library root regardless of mount point.

```json
{
  "name": "On-Call Essentials",
  "description": "Must-know emergency cases for junior trainees",
  "author": "Dr Smith",
  "created_at": "2026-02-14T10:00:00Z",
  "updated_at": "2026-02-20T15:30:00Z",
  "modality": "mixed",
  "specialty": ["emergency", "oncall"],
  "min_level": "ST1",
  "recommended_levels": ["ST1", "ST2"],
  "cases": [
    {
      "path": "@library/emergency/01_Pneumothorax",
      "notes": "Classic tension pneumothorax - note mediastinal shift"
    }
  ]
}
```

### Exam Config JSON

Stored in `exams/`. Saved by Exam Builder; can also be created manually.

```json
{
  "exam_name": "ST1 On-Call Assessment Feb 2026",
  "name": "ST1 On-Call Assessment Feb 2026",
  "results_path": "legacy",
  "course_type": "exam",
  "modality": "mixed",
  "marking_scheme": "rcr_cr2b",
  "time_limit_minutes": 75,
  "eligible_levels": ["ST1", "ST2"],
  "cases": [
    {
      "id": "01_Pneumothorax",
      "path": "@library/emergency/01_Pneumothorax",
      "diagnosis": "Tension pneumothorax"
    }
  ]
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `exam_name` | Yes | Unique name; used as primary key in the database |
| `results_path` | Yes | Legacy field; submissions go to the tracking DB |
| `marking_scheme` | No | `"legacy"` (default), `"dimension"`, or `"rcr_cr2b"` |
| `time_limit_minutes` | No | Total duration. Omit for untimed. |
| `eligible_levels` | No | Array of trainee levels; omit for unrestricted |
| `eligible_trainee_ids` | No | Array of specific trainee IDs; overrides `eligible_levels` |

### License File (`.muninn-license`)

```json
{
  "license_key": "MUNINN-AB12-CD34-EF56-GH78",
  "license_type": "standard",
  "department_name": "St George's Radiology",
  "issued_at": "2026-02-01T00:00:00Z",
  "expires_at": "2027-02-01T00:00:00Z",
  "grace_period_days": 30,
  "signature": "base64-encoded-ed25519-signature"
}
```

---

## Directory Structure

### Department Root (Network Share)

```
department_root/
├── library/
│   ├── .muninn/
│   │   └── case_index.db             # FTS5 search index
│   ├── playlists/
│   │   └── *.json
│   ├── courses/
│   │   └── ct-abdomen/
│   │       └── 01_Acute_Appendicitis/
│   │           ├── case_data.json
│   │           ├── key_slices.json
│   │           ├── STUDY_NOTES.md
│   │           └── 01_Axial_CT/
│   │               └── *.dcm
│   └── consultants/
│       └── dr-smith/
├── exams/
│   └── *.json
├── reports/
└── tracking/
    ├── muninn_tracking.db
    └── backups/
        ├── daily/
        ├── weekly/
        └── manual/
```

### Local App Data

**macOS:** `~/Library/Application Support/com.muninn.dicom/`

**Windows:** `%APPDATA%\com.muninn.dicom\`

Contains `muninn.db` (local attempt history and playlist progress) and `config.json` (app settings).

---

## Performance

- **WAL mode**: SQLite uses Write-Ahead Logging for concurrent multi-user access
- **Busy timeout**: 30 seconds to handle network latency spikes
- **Retry logic**: 5 retries with exponential backoff on transient failures
- **Series caching**: All DICOM metadata is stored in `case_data.json` — Muninn never re-reads DICOM headers at load time, making network-share performance acceptable even for large series
- **FTS5 index**: Full-text search scales to thousands of cases with sub-second response

---

## Security

### PIN Hashing

Trainee PINs are hashed using Argon2id (memory: 19MB, iterations: 2, parallelism: 1, salt: 16 bytes random). The original PIN cannot be recovered — administrators can only reset PINs.

### License Verification

Licenses use Ed25519 digital signatures. The public key is embedded in the app binary; the private key is held by the license issuer and never distributed. Verification is entirely offline after initial import.

Signed payload format:
```
{license_key}|{license_type}|{department_name}|{issued_at}|{expires_at}
```

### Data Protection

- All data stays on local machines or the department network share
- No cloud services or external API calls (except optional AI feedback providers)
- DICOM files are used as-received from PACS (anonymized per hospital policy)
- Audit logs provide a full chain of custody for assessment data
