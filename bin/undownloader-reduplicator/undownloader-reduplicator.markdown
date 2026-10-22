# undownloader-reduplicator

#### mission statement
If your media libraries have duplicates we should reduplicate them soon.
This is also called proper backup.
Replicate the duplicates at the proper file system locations so you can find them.
You like one song, now you have two song.
If you're not happy the uninstallation is easy – just desynchronize the target directory.

## Usage

Run from a media tree, or pass paths to the wrapper.

Match = 11-char YouTube id from media **PURL**, **filename**, **directory name**, or video **`.info.json`**. PURL and json must agree. Playlists do not count. `*.info.json.original.json` is ignored.

`video id: PATH` in `./reduplicator-matches.txt`. Newlines in paths are stored as `\n`.

---

## Wrapper

```bash
undownloader-reduplicator ACTIVE
undownloader-reduplicator REFERENCE ACTIVE [TARGET]
```

Default reference: `🔴.undownloaded`. Active is cleaned. Reference is never moved.

1. List both trees → `reduplicator-matches.txt` (old file → `matches-DATE.backup.txt`).
2. Simulate filter|move. Stop if `errors.log` is non-empty or exit 99 (nothing to move).
3. `--fast`: reuse `reduplicator-matches.txt`, simulate only.
4. Confirm, then move. Logs stay in `ACTIVE/undownloader-reduplicator-DATE/`.

```bash
undownloader-reduplicator --fast /media/active
```

---

## Manual

```bash
cd /media/reference && undownloader-reduplicator-index > reduplicator-matches.txt
cd /media/active    && undownloader-reduplicator-index > reduplicator-matches.txt

cd /media/active
run=./undownloader-reduplicator-$__current_date
undownloader-reduplicator-filter --run-dir "$run" /media/reference/reduplicator-matches.txt \
  | undownloader-reduplicator-move --simulate --run-dir "$run" \
      --reference /media/reference/reduplicator-matches.txt - ./removed
```

Same pipe without `--simulate` to move. Add `--enable-media-hashing` to skip ffmpeg hashes (size/`cmp` still run).

---

## Run directory (`ACTIVE/undownloader-reduplicator-DATE/`)

| File | Role |
|---|---|
| `warnings.log` / `errors.log` | all warnings / errors |
| `orphans.log` | json with no media; media with no id (error) |
| `json.log` | PURL / name / json disagree |
| `layout.log` | mixed leaf, ignored directory id |
| `filter.log` | skip if path is inside reference |
| `media-hash-match.log` / `media-hash-no-match.log` | encode compare |
| `defect-media.log` | stream decode failed |
| `move.log` | simulate/real from → to |
| `matches-only-in-active.txt` | id only on active |
| `matches-only-in-reference.txt` | id only on reference |
| `matches-extension-*.txt` | list split by suffix |
| `non-reduplicator-matches.txt` | dirs with no usable id |

Move default target: `../undownloader-reduplicator-duplicates-DATE`. Existing dest name → `name_1`. Root `.` is never moved.

---

## Rules (list)

Accept: 1 media ± 1 json; or same stem `id.mkv`+`id.mka` + 1 json; or media with PURL/filename only.

Warn: mixed ids in one leaf (directory name ignored); json id not claimed by media; name ≠ PURL/json.

Error: media file with no resolvable id.
