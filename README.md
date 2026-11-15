# EQ Raid Loot Helper

**Keep your raid's loot process. Lose the scrollback hunt.**

A Windows companion for EverQuest raid leaders and master looters. It reads a
character's log, follows bids and rolls, and keeps winners, looters, and items
together so you can spend less time searching chat.

**Read-only game integration.** No key broadcasting, game commands, or automatic
looting. Copy buttons prepare text on the clipboard; you decide what to paste.

> **Early development:** this repository is being set up. Source upload and
> packaged downloads are not available yet. The current development version is
> a C# WinForms application launched with Windows PowerShell 5.1, not an installer.

## What it helps with

| During the raid | The helper |
| --- | --- |
| Collecting `#item`, `#item2`, or another bid word | Separates OK bids from mistakes and removes corrected names from the rebid list. |
| Rolling against a numbered bid table | Tracks winners and flags rerolls for zero, the top number, or someone who already won that table. |
| Calling `random 777` with high or low rolls winning | Builds a list from matching rolls observed during the call's window. |
| Working down a loot list | Shows the next person and puts confirmed loot beside its roller in loot order. |
| Checking earlier rounds | Lets you select individual events and group their records on screen. |
| Wrapping up the night | Writes chronological text and CSV reports. |

## Getting started

These are the steps for running the development version once you have the
application files.

1. Use **Windows** with **Windows PowerShell 5.1**.
2. In EverQuest, enable logging with `/log on` on the character whose log you
   want to follow. That character must receive the raid chat and rolls.
3. Keep the application files together in a folder you can write to.
4. Open Windows PowerShell in that folder and run:

   ```powershell
   powershell -NoProfile -STA -File .\RaidLoot.ps1
   ```

On first run, the helper looks for running `eqgame.exe` clients and chooses the
most recently modified character log from their install folders. An existing
saved folder and character-log selection take priority.

Use **Browse EQ...** if detection cannot find your install, then pick the
EverQuest folder containing `Logs`. Check the **Log** dropdown before relying
on the results: the newest log is not necessarily the character receiving all
the raid messages.

## Two loot methods

### Bid word and numbered table

The caller asks interested players to say a word such as `#item`, then posts a
number table:

```text
/rsay #item if interested
0=reroll/1-3=Playerone 4-6=Playertwo 7=reroll /rand 0-7
```

The helper follows the caller's rolls, keeps one winner per main for that table,
and matches chest loot to winners. Main/alt links can be recorded when an alt
loots for a main.

Bid mistakes appear under **FAIL**, after the **OK** bids, with names alphabetical
within each group. **Copy rebid names** includes only names still needing a
correction, split into chat-sized pages when needed.

### High-roll or low-roll order

The caller announces something like:

```text
random 999 top roll down wins in order
/random 777 lowest wins
```

The helper collects matching `0-N` rolls after the instruction. Collection closes
on the caller's `CLOSED`, when a list is posted, or through the 90-second
final-call fallback as log messages are processed.

- Players' first matching rolls count. The current caller's multiple rolls are
  retained to support the observed raid format.
- Wrong-range, extra, and late rolls are shown as not counted.
- Rollers appear A-Z during collection, then in the prescribed order once looting
  begins.
- **Copy hi-lo** and **lo-hi** offer numbered pages under EQ's per-line limit.
- **Flip next-in-line order** lets you correct an unclear or misread instruction.
- The looter must match the character who rolled; main/alt substitution is not
  used for this method.

Recognizable calls can come from anyone in raid. **Last caller** follows whoever
is currently running the process, rather than requiring a fixed raid leader.

### Rolling for someone who asked in voice

Only the **current caller** can assign a roll to someone else. The helper cannot
hear voice chat: announce the assignment in **raid chat before rolling**.

For example, while a `random 555` call is open, Dreek types:

```text
/rs rolling for Playerx
/random 555
/random 555
```

The first valid `0-555` roll goes to **Playerx**; the next roll goes to **Dreek
himself**. The assignment is used once, so announce `rolling for Otherplayer`
before rolling for another person.

- Instructions from anyone except the current caller are ignored.
- Other players' rolls do not consume the assignment. A wrong-range caller roll
  is flagged and leaves the assignment waiting for a valid roll.
- If Playerx already has an accepted roll, the proxy attempt is flagged as an
  extra roll rather than giving Playerx another entry.
- Lists and loot matching use **Playerx**, while the display/report records who
  physically rolled. Playerx must be the character looting.
- A new random call, closure, or Clear discards any unused assignment.

This requires the updated helper build; the previously running version does not
understand these instructions.

## Finding your place

- **Sessions** follows the latest event by default. Tick earlier events to view
  them together, or choose **Follow latest event** to return to live tracking.
- **Clear** resets the current screen and round from now without deleting the
  night's report history. The clear time survives a restart.
- **Restart** replays tonight's log to rebuild state; it does not restart EQ.
- Confirmed items appear on their winner's row. Possible alt matches remain
  separate suggestions until you confirm them.

## Local data and reports

Settings, main/alt mappings, and reports are stored beside the application:

| File or folder | Purpose |
| --- | --- |
| `settings.txt` | Selected log folder, character log, last caller, and display settings |
| `mains.txt` | Main-to-alt mappings |
| `reports\raidloot-yyyy-MM-dd.txt` | Human-readable nightly report |
| `reports\raidloot-yyyy-MM-dd.csv` | Spreadsheet-friendly nightly report |

The original EQ log is not modified. Reports remain chronological even when the
screen is arranged by loot order.

Do not post raw character logs, personal settings, or raid reports when sharing
the application or reporting an issue. A small, anonymized example is usually
more useful.

## Limits worth knowing

This is a helper, not the loot authority. It only knows messages present in the
selected log and recognizes the raid formats it has been built and tested for.
It cannot establish every guild eligibility rule or settle tied rolls.

The chest timer is an estimate based on the first observed chest loot or item-left
message, not a direct measurement of chest creation. Late-loot/FFA labels and
possible alt matches are prompts for review, not decisions.

## Development

The development version uses C# WinForms and Windows PowerShell 5.1. Run the
window-free regression checks from the application folder:

```powershell
powershell -NoProfile -File .\Test-RaidLoot.ps1
```

For bugs or new raid formats, use [GitHub Issues](https://github.com/kadwen812/EQRaidLootHelper/issues)
and describe the expected result, the actual result, and an anonymized log sample.

## License

[MIT](LICENSE) - copyright (c) 2026 kadwen812. Free to use, modify, distribute,
and use commercially, subject to the license notice. Provided without warranty.

An independent community project; not affiliated with or endorsed by EverQuest
or its publisher.
