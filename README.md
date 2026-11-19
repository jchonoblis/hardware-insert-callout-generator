# HARDWARE INSERT CALLOUT GENERATOR

Static web app (GitHub Pages ready) that builds ASME Y14.5-style drawing notes and a live, to-scale cross section for:

- Helical threaded inserts: MS21208 (inactive) replaced by MS122xxx / MS124xxx per NASM33537
- Self-clinching nuts: M45938/1 (MIL-N-45938/1B, now NASM45938/1)
- Self-clinching flush-head studs: M63540/1 (MIL-S-63540/1A, now NASM63540/1)
- Stainless-sheet clinch hardware with no mil PN (commercial only): PEM SP nuts and PEM FH4 studs, used for A304

Threads: 4-40, 6-32, 8-32, 10-32, 1/4-20, 5/16-24, 3/8-24, 1/2-20 (from `data/threads.csv`). 5/16-24, 3/8-24 and 1/2-20 offer threaded inserts and clinch nuts; no mil self-clinching stud exists in these threads. 5/16-18 is not offered for any hardware type; an old 5/16-18 link opens on 5/16-24 with a notice. Substrates: 5052-H32 (default), 6061-T6, A304 (annealed), A1008 CS Type B.

## How it works
- Every value printed in a note comes straight from a CSV row in `data/`. Nothing is computed into the note.
- Note symbols (v1.4), for pasting into SolidWorks notes: `<HOLE-SINK>` countersink, `<HOLE-DEPTH>` depth, `Ø` diameter, `°` degree. Per ASME Y14.5, the symbol precedes its dimension with no space.
- Countersinks are always called out with diameter and included angle (ASME Y14.5-2018 4.5.12). Inserts print `<HOLE-SINK>Ø.230-.260 X 120°`, with the angle from `csink_angle` (NASM33537). The angle tolerance is not printed; the drawing title block covers it.
- Values are shown with at most 3 decimal places (half-up rounding, so drill #7 .2010 prints as .201). A clinch hole nominal with more places has both limits rounded inward to stay inside the spec range.
- Clinch nut and stud holes (v1.7) print the standard drill and its decimal, the same style as inserts: `4X DRILL #3 (Ø.213) THRU`. Drills come from `data/clinch_drills.csv`; where no standard inch drill falls inside the spec range (.137, .163 and .344 holes) the app shows an info line. The 6-32 stud hole (.137-.140) uses #28 (Ø.1405), the closest standard inch drill (.0005 over the spec maximum; no inch drill falls inside the range), with an info line.
- Selections that break a spec rule are flagged, with one-click fixes, but still allowed.
- Blind insert holes must keep MIN DEPTH plus the 118 deg drill point inside the material.
- Clinch hardware is CRES only. Carbon steel and aluminum rows stay in the CSVs as a spec record but the app never offers them.
- Auto-select uses mil CRES whenever it is rated for the substrate. A304 exceeds the mil CRES limits, so the app uses PEM FH4 studs (rated to 92 HRB) and PEM SP nuts (rated to 90 HRB, with a warning to confirm sheet hardness). These print the PEM PN and CAGE 46384 and are tagged "Commercial PN, no mil equivalent".
- Thickness suggestions snap to standard sheet and plate stock for the selected material (gauge values for A304 and A1008, decimal sheet and plate for aluminum), stored in `data/materials.csv` with sources. Non-standard entries show the nearest standard thicknesses.
- For mil parts, the equivalent commercial PEM part number is shown grayed out for reference only and is never copied into the note. The reference PN itself is selectable (one click selects it) so it can be copy-pasted. Threaded insert notes append UNCOATED to the PN (e.g. PN: MS124655 UNCOATED) to call out the uncoated version, since these inserts come in several coatings.
- Stud length (v1.7.3): Auto picks the shortest L that covers 2X sheet + NAS1149 washer + lock nut. That length is a suggestion only; a shorter stud is accepted with an info line. The app warns only when L does not clear (protrude past) the sheet.
- Stud spec values include PEM push-out, torque-out and pull-thru averages (`data/pem_fh_performance.csv`; FH4 values from its own CSV). These are PEM averages for reference, not spec minimums.
- Quantity (v1.7.4): pick 1-50 from the dropdown or type any whole number, same combobox as substrate thickness.
- Hardware size (v1.7.7): the INSTALL line states the size. Studs and inserts print THREAD X LENGTH (`INSTALL 10-32 X .625 SELF-CLINCHING STUD`, stud length L; `INSTALL 10-32 X .190 THREADED INSERT`, insert nominal length Ln). Clinch nuts print the PEM shank code instead of a length (`INSTALL 10-32 SHANK 2 SELF-CLINCHING NUT`), from the `shank_code` column (last dash of the matching PEM PN).
- All caps (v1.7.8): every drawing-note line is uppercase, including the capital X in THREAD X LENGTH.
- Page stays still (v1.7.9): opening or picking from the quantity and thickness dropdowns scrolls only the list, never the page.
- Dropdown wheel scrolling (v1.7.10): the thickness and quantity lists take mouse-wheel scrolling as soon as they open and do not pass it to the page.
- 6-32 (v1.8.0): inserts, CRES nuts, CRES studs, PEM SP-632 nuts and FH4-632 studs, with the NAS1149CN632 washer and MS21044C06 nut for stud length. The thread list and thread geometry come from `data/threads.csv`; the substrate list comes from `data/materials.csv`.
- 5/16-18 (v1.9.0): removed from the app for all hardware types. The verified rows stay as spec record with `in_app_scope` N: MS21208 inserts MS122082 to MS122242 in `data/hardware_ms21208.csv`, and M63540/1-51 to -56C studs in `data/hardware_m63540_1.csv` (MIL-S-63540/1A and Amendment 2 Table IV), with their drill, washer/nut stack and PEM performance rows. No 5/16-18 clinch nut exists (M45938/1 covers 5/16-24 only).
- 5/16-24 (v1.9.0): MS21208 inserts MS124657, MS124697, MS124737, MS124777, MS124817 (MS21208F5-10 to -30; Heli-Coil 1191-5CN312 to -5CN938), drill 21/64 (Ø.3281), countersink Ø.360-.390 (NASM33537 Table IV). CRES clinch nuts M45938/1-21C (sheet .091-.124) and -22C (.125 up), hole .413 with drill Z, edge .375 (MIL-N-45938/1B); PEM CLS-0524 cross reference; commercial-only SP-0524-1/-2 for A304. No 5/16-24 clinch stud.
- 3/8-24 and 1/2-20 (v1.9.0): MS21208 free-running tanged CRES helical inserts, 1D-3D. 3/8-24: MS124658, MS124698, MS124738, MS124778, MS124818 (MS21208F6-10 to -30), drill 25/64 (Ø.3906), countersink Ø.420-.450. 1/2-20: MS124660, MS124700, MS124740, MS124780, MS124820 (MS21208F8-10 to -30), drill 33/64 (Ø.5156), countersink Ø.560-.590. Clinch nuts: M45938/1-23C/-24C (3/8-24, drill 1/2) and -25C/-26C (1/2-20, drill 21/32) per MIL-N-45938/1B Table I, with PEM CLS-0624/-0820 cross references. In A304 sheet, 3/8-24 uses commercial-only PEM SP-0624-1/-2 (no mil equivalent); 1/2-20 has no SP nut, so it warns and recommends an insert. No mil or PEM stud is made in 3/8-24 or 1/2-20, so the thread buttons are disabled for studs; a shared link that selects one shows a warning with one-click fixes.
- The selection lives in the URL hash, so a link reproduces a note.

## Run locally
```
python3 -m http.server 8000
# open http://localhost:8000
node tests/notes.test.mjs   # golden-note tests
```

## Add hardware
Add rows to the matching CSV in `data/` with `in_app_scope=Y` (new thread sizes also need a row in `data/threads.csv`) and a `sources` entry from `data/sources.csv`. See `data/README.md` for column rules.

## Disclaimer
Drafting aid only. Verify every note against the current governing specification before release.
