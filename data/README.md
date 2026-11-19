# Hardware data (v0.18, confirmed-only)

| File | Rows | Content |
|---|---|---|
| hardware_ms21208.csv | 55 | Helical inserts 4-40, 6-32, 8-32, 10-24, 10-32, 1/4-20, 1/4-28, 5/16-18 (spec record only), 5/16-24, 3/8-24, 1/2-20; 1D-3D |
| hardware_m45938_1.csv | 64 | Clinch nuts 2-56 through 1/2-20 (all Table I dashes 1-29), incl. 1/4-28 and 5/16-24; carbon, CRES, aluminum dashes |
| hardware_m63540_1.csv | 112 | Flush-head studs 2-56 through 5/16-18 (5/16-18 spec record only); carbon and CRES |
| hardware_pem_sp.csv | 18 | PEM SP (PEM 300) clinch nuts 4-40, 6-32, 8-32, 10-32, 1/4-20, 5/16-24, 3/8-24; commercial only, no mil PN |
| hardware_pem_fh4.csv | 45 | PEM FH4 flush-head studs 4-40, 6-32, 8-32, 10-32, 1/4-20; commercial only, no mil PN |
| materials.csv | 4 | 5052-H32, 6061-T6, A304, A1008 CS Type B hardness, compatibility and standard stock thicknesses |
| pem_fh_performance.csv | 28 | PEM FH/FHS stud push-out, torque-out and pull-thru averages |
| hardware_stud_stack.csv | 6 | Stud length basis per thread: NAS1149 CRES washer and MS21044C / MS17830-4C / MS17830-5C lock nut |
| clinch_drills.csv | 16 | Standard drill for each clinch nut and stud spec hole |
| threads.csv | 9 (8 in app) | Thread sizes offered in the app: major diameter, threads per inch, series, PEM thread code (blank for 3/8-24 and 1/2-20, which have no PEM stud) |
| sources.csv | 39 | Source IDs referenced in each row |

Rules
- `note_pn` / `note_cage` are what the drawing note prints (current mil PN, CAGE 80205). `legacy_pn` / `legacy_cage` are kept for reference.
- Exception: rows with `commercial_only` = Y (SP nuts, FH4 studs) print the PEM catalog PN and PEM CAGE 46384, because PEM's MIL cross reference lists no military PN for SP, FH4 or FHP. The app uses them only when no mil part is rated for the substrate (A304), or when the user selects them.
- Every row is CONFIRMED against the spec or standard. Cross-source checks: insert FP/FB/H match NASM33537 and Heli-Coil and pass the NASM33537 formulas. Stud dash grid matches vendor listings.
- Blank cells are values that are illegible or defective in the source; nothing was guessed. Vendor values appear only in `*_vendor_ref` columns and must not be used in notes.
- Stud length L follows the PEM FH datum: overall length from the top (flush) face of the head.
- Stud `unthreaded_S_max` is MIL-S-63540/1A Table I "S MAX": the maximum unthreaded length under the head (renamed from `head_height_max` in v0.7). The spec gives no head thickness.
- Above 1/4, the app covers 3/8-24 and 1/2-20 helical inserts (v0.13) and clinch nuts (v0.14), and 5/16-24 helical inserts and clinch nuts (v0.16). No self-clinching stud exists in 3/8-24 or 1/2-20 (see below). 5/16-18 (all types; inserts and studs kept as spec record, v0.18), 5/16-24 studs and 3/8-16 are out of scope. 5-40 inserts are excluded because no drill recommendation was confirmed. M63540/1-57 (listed by PEM but not in MIL-S-63540/1A) is excluded.

Hardware material rule (v0.10)
- CRES hardware only. The app never offers carbon steel or aluminum nuts and studs. Those rows stay in `hardware_m45938_1.csv` and `hardware_m63540_1.csv` (and their `m45938_carbon`, `m45938_aluminum`, `m63540_carbon` columns in `materials.csv`) as a complete record of the spec, but are filtered out in the app.
- Allowed: mil CRES (M45938/1-xxC, M63540/1-xxC) and PEM hardened stainless SP and FH4 (commercial only). Auto-select uses mil CRES whenever it is rated, including at its limit, and uses SP or FH4 only when no mil CRES part is rated.

Standard stock thicknesses (v0.11)
- `materials.csv` columns: `std_thk_in` (ascending decimal inches, semicolon separated), `std_thk_labels` (gauge or form for each value, same order), `std_thk_basis`, `std_thk_sources`.
- 5052-H32: sheet .020, .025, .032, .040, .050, .063, .080, .090, .100, .125, .190 and plate .250, .375 (Chatham Steel); .160 (Continental Steel); .500 (SendCutSend). Aluminum is ordered by decimal thickness, not gauge.
- 6061-T6: sheet .025 through .190 (Chatham Steel). Plate .250, .313, .375, .500, .625, .750, 1.000 is stocked as 6061-T651 (Chatham Steel).
- A304: stainless sheet gauges 28 GA .015 through 7 GA .1874 as decimal values (Chatham Steel sheet table, Type 304/304L 2B), and plate 1/4 through 1 in (Chatham Steel plate table). 3/16 plate is listed once, as 7 GA .1874.
- A1008 CS Type B: Manufacturers' Standard Gauge nominal values 24 GA .0239 through 10 GA .1345 (Shaw Steel gauge chart; Russel Metals lists 10 GA as .1344). Stock range 24 GA to 10 GA per Westfield Steel. Cold rolled sheet is not stocked as plate, so thicker A1008 requirements are shown as non-standard.
- Lists are capped at 1.000 in (app scope). Gauge decimals are nominal; mill tolerance applies.
- App use: every "Set thickness to" suggestion snaps to the smallest standard thickness that meets the requirement (FH4 above its maximum: the largest standard thickness inside the PEM range). If no standard thickness works, the exact value is offered and labeled non-standard. An entry within .0005 in of a standard value counts as standard (for example .060 matches 16 GA .0598).

Commercial-only PEM rows (SP, FH4)
- Source: PEM bulletin SS, Fasteners For Use In Stainless Steel Sheets, rev 10/13/26. SP dimensions pg SS-3, FH4 pg SS-10, material pg SS-15, performance pg SS-23/24, Dos and Don'ts pg SS-27. Checked against the page images.
- SP: rated for stainless sheet HRB 90 / HB 192 or less. Shank code chosen as the longest shank whose recommended minimum sheet thickness fits; PEM publishes no maximum sheet thickness. Performance values are PEM averages in 304 stainless, stored in `*_avg` columns, not spec minimums.
- FH4: hardened 400-series stainless, rated for stainless sheet HRB 92 / HB 202 or less. Sheet thickness range .040-.095 (1/4-20: .062-.117); PEM notes performance may be reduced in thicker sheets. Length L is overall from the flush head face (PEM drawing). Requires the PEM special anvil with raised ring. FHP (A286) is not included.
- Only 4-40, 6-32, 8-32, 10-32 and 1/4-20 are included (app scope). Finish suffixes are omitted.
- 6-32 (v0.12): SP-632-0/1/2 (A .030/.038/.054, min sheet .030/.040/.056, hole .1875, C .187, E .280, T .070, edge .22; 8500/9500/10500 lb install, 140/170/340 lb pushout, 18/24/28 in-lb torque-out) and FH4-632-4 through -24 (sheet .040-.095, hole .137, max hole in attached parts .157, H .206, S .090, edge .250; 22 in-lb nut torque; 9500 lb install, 540 lb pushout, 27 in-lb torque-out, 1350 lb pull-thru). Checked against the page images.

Material notes
- A304: ASTM A240 maximum 92 HRB exceeds every M45938/1 and M63540/1 limit. FH4 (92) is at its limit and compliant; SP (90) is below the A240 maximum, so the app warns to confirm sheet hardness or use a helical insert.
- A1008 CS Type B: 70 HRB or less is a typical value from the nonmandatory ASTM A1008 Table X3.1, not a spec limit. CRES nuts and studs (70) are at their limit and are auto-selected under the CRES-only rule, with a note to confirm actual sheet hardness.

Open items
- MIL-N-45938/1B Amendment 1 (1997) has not been reviewed. NASM45938/1 and NASM63540/1 current revisions are paywalled and not reviewed.
- 10-24 insert tapped-hole minimum minor diameter is defective in the source PDF.
- Nut shank tolerance header is partly illegible.

Commercial PEM reference (reference only, never printed in the note)
- `commercial_pem_pn` and `commercial_pem_basis` columns in all three hardware CSVs. Matched on thread, material, shank or length, and hole size from the PEM CL and FH data sheets. PEM's MIL cross reference maps the families (45938/1 to CLA/CLS/CLSS, 63540/1 to FH/FHS) but not dash numbers, so the basis column lists dimension differences.
- For commercial-only rows, `commercial_pem_pn` equals `note_pn`.
- In hardware_ms21208.csv, `commercial_pem_pn` holds the Heli-Coil commercial PN (1185 coarse / 1191 fine, plain finish) cross-referenced from the MS122xxx/MS124xxx PN (S-HCXREF).
- Blank for all inserts (no PEM helical insert) and for nuts without a matching PEM shank length (M45938/1-1A, -19A, -20A).

Clinch-hardware drills (v1.7)
- `clinch_drills.csv` maps each nut and stud spec hole (`hole_dia_in_sheet`, `hole_tol`) to a standard inch drill (number, letter or fractional) and its decimal size (S-DRILL). Nut and stud notes print `DRILL <size> (Ø<decimal>) THRU`, the same style as insert notes.
- The drill is the standard inch drill at or just above the spec minimum. Three exceptions: .137 (6-32 stud) uses #28 (.1405), five ten-thousandths over the spec maximum, because no standard inch drill falls inside .137-.140 (#29 .1360 is .0010 under the minimum; #28 is the closest). Metric drills are not used: inch hardware gets inch drills. .163 uses #19 (.166), at the spec maximum, because no standard inch drill falls inside .163-.166 below it; .344 uses 11/32 (.3438), two ten-thousandths under the spec minimum, because no standard inch drill falls inside .344-.347. The app shows an info line for these and for .249 (1/4, .250).

## shank_code (v1.7.6)
- `shank_code` column in `hardware_m45938_1.csv` and `hardware_pem_sp.csv`: PEM shank code, taken from the last dash of `commercial_pem_pn` (PEM CL/SP data sheets). Printed on the nut INSTALL line instead of shank length A. Blank only for out-of-scope rows with no PEM match.

## Thread list (v1.8.0)
- `threads.csv` drives the app thread buttons and the cross-section thread geometry (major diameter and threads per inch). Only rows with `in_app_scope` = Y are offered. Values match the `major_dia` / `tpi` columns of the hardware CSVs (NASM33537 Table I) and the PEM thread codes. The substrate material list comes from `materials.csv`.
- Adding a thread means adding its row here, setting `in_app_scope` = Y on its rows in the hardware CSVs, and adding its washer/nut to `hardware_stud_stack.csv` and any new hole size to `clinch_drills.csv`.

## 6-32 (v1.8.0)
- In app scope: MS21208 inserts (MS122078, MS122118, MS122158, MS122198, MS122238; 1D-3D), M45938/1-8C and -9C CRES nuts, M63540/1-12C through -19C CRES studs, SP-632 nuts and FH4-632 studs. Carbon steel and aluminum 6-32 rows stay as spec record under the CRES-only rule.
- Stud stack: NAS1149CN632 washer (No. 6, CRES, .032 +/-.004, AN960C6 equivalent) and MS21044C06 lock nut (height .160-.188).
- Aluminum nuts M45938/1-10A, -11A (6-32) and -18A (10-32) had no legible spec edge distance; PEM CLA values (.27, .31; S-PEMCL pg 5) were added to `min_edge_dist_vendor_ref` for reference only.

## 3/8-24 and 1/2-20 inserts (v1.9.0, data v0.13)
- Inserts and clinch nuts (v0.14, see next section). No stud rows exist, so the app disables these thread buttons for studs (`threadTypes()` in `src/logic.js` reads which hardware CSVs have in-scope rows).
- PNs (S-KATO, S-HCXREF): 3/8-24 1D/1.5D/2D/2.5D/3D = MS124658/698/738/778/818 = MS21208F6-10/15/20/25/30 = Heli-Coil 1191-6CN375/562/750/938/1125. 1/2-20 = MS124660/700/740/780/820 = MS21208F8-10/15/20/25/30 = Heli-Coil 1191-8CN500/750/1000/1250/1500.
- Dimensions: NASM33537 Table IV, checked against the page images (p9 for 3/8-24, p10 for 1/2-20). 3/8-24: Ln .375/.562/.750/.938/1.125; FP .625/.812/1.000/1.187/1.375; FB .542/.729/.917/1.104/1.292; H .420/.600/.790/.980/1.170; tapped-hole minor dia .3840 min, .3937 max (2B); countersink Ø.420-.450. 1/2-20: Ln .500/.750/1.000/1.250/1.500; FP .800/1.050/1.300/1.550/1.800; FB .700/.950/1.200/1.450/1.700; H .550/.800/1.050/1.300/1.550; minor dia .5108 min, .5223 max (2B); countersink Ø.560-.590.
- Every FP, FB and H value was checked against the NASM33537 Table III formulas for sizes over .3125 (FP = Ln + 6P, FB = Ln + 4P, H = Ln + 1P). One discrepancy: 3/8-24 2.5D FP prints 1.185 in the NASM33537 retype, but the formula gives .9375 + .2500 = 1.1875 and Heli-Coil Table V prints 1.187. The CSV stores 1.187 and records this in the row's `verification` field.
- Drills (S-HC Table V): 3/8-24 25/64 (.3906) and 1/2-20 33/64 (.5156), the same in aluminum and steel. Both fall inside the tapped-hole minor diameter range.
- Minimum thru-hole thickness with countersink = H, without countersink = Ln (NASM33537 sec 8). Insert set-down 0.75P-1.5P with countersink and 0.25P-0.5P without (sec 11): 3/8-24 .0312-.0625 / .0104-.0208, 1/2-20 .0375-.0750 / .0125-.0250.
- Standard stock thickness lists stop at 1.000 in, so longer inserts (for example 1/2-20 3D, which needs 1.550 thru) get non-standard thickness suggestions.

## 3/8-24 and 1/2-20 clinch nuts (v1.9.0, data v0.14)
- Mil nuts exist. MIL-N-45938/1B Table I (S-45938, checked against the page images) lists 3/8-24 UNF dashes 23/23C/23A (A .120, sheet .125-.249) and 24/24C/24A (A .235, sheet .250 and up), and 1/2-20 UNF dashes 25/25C/25A (A .120, sheet .125-.249) and 26/26C/26A (A .235, sheet .250 and up). 3/8-24: hole .500 +.003/-.000, C .498, E .562 +/-.015, T .266 +/-.015, min edge .438. 1/2-20: hole .656, C .654, E .812, T .359, min edge .625. Dashes 21/22 (5/16-24) stay out of scope.
- Table II (approx install force / carbon and CRES push-out / torque-out / aluminum push-out / torque-out): 3/8-24 10000 / 500 / 250 / 325 / 200 lb, in-lb. 1/2-20 14000 / 800 / 500 / - / -; the spec gives no aluminum values for .500, so those cells are blank.
- These dashes are active and stocked (PEM NASM cross reference S-PEMMIL; distributors list M45938/1-23C as CLS-0624-2 and -26C as CLS-0820-2).
- PEM equivalents (S-PEMCL, S-UNICORP-CLS): CLS-0624-2/-3 = -23C/-24C, CLS-0820-1/-2 = -25C/-26C, carbon S-0624-2/-3 and S-0820-1/-2. PEM makes no CLA (aluminum) nut in these threads. PEM dimensions differ slightly from the mil table (3/8-24 C .499, E .560, T .270, edge .44; 1/2-20 C .655, E .810, T .360, edge .63); the CSV stores the mil values and lists the differences in `commercial_pem_basis`.
- CLS-0624-1 (A .087, sheet .090-.124) has no mil equivalent and is not stored, matching the other threads, which carry only mil-matched CLS shanks. Sheet below .125 gets the minimum-thickness warning.
- Drills (S-DRILL): 1/2 (.5000) for the .500 hole, at the spec minimum; 21/32 (.6562) for the .656 hole, inside the +.003 tolerance.
- Stainless sheet (A304): commercial-only PEM SP-0624-1 (A .087, sheet .090) and SP-0624-2 (A .120, sheet .125), PEM pg SS-3: hole .500, C .499, E .560, T .270, edge .44, 90 HRB rating. Performance in 304 stainless (pg SS-23): install 16000/20000 lb, push-out 600/700 lb, torque-out 300/370 in-lb (averages). No mil equivalent, the same as the other SP rows. PEM makes no SP nut in 1/2-20, so 1/2-20 nuts in A304 get the "helical insert recommended" warning.
- No self-clinching stud exists in 3/8-24 or 1/2-20. MIL-S-63540/1A and NASM63540/1 stop at 5/16-18 (S-63540). PEM's FH/FHS/FHA studs stop at 5/16-18, FH4 at 1/4-20 and HFH/HFHS at 3/8-16 (S-PEMFH); 3/8-24 appears in the PEM bulletin only as a dog-point option thread. The stud warning explains this and offers an insert, a clinch nut or 1/4-20.

## 5/16-18 inserts (v1.9.0, data v0.14; removed from the app in v0.17)
- Not offered in the app: the five rows have `in_app_scope` N and stay as a verified spec record. The 5/16-18 thread is out of the app entirely (v0.18; `threads.csv` `in_app_scope` N).
- PNs (S-KATO, S-HCXREF): 1D/1.5D/2D/2.5D/3D = MS122082/122122/122162/122202/122242 = MS21208C5-10/15/20/25/30 = Heli-Coil 1185-5CN312/469/625/781/938. CAGE 80205 (MS122xxx) and 96906 (legacy MS21208), the same as the other inserts.
- Dimensions: NASM33537 Table IV p9, .3125-18 column, checked against the page image. Ln .312/.469/.625/.781/.938; FP .801/.957/1.113/1.269/1.425; FB .534/.690/.846/1.002/1.158; H .370/.530/.680/.840/.990; tapped-hole minor dia .3245 min, .3372 max (2B); countersink Ø.380-.410. Heli-Coil Tables V and VII give the same FP, FB, H, countersink and minor diameter values.
- 5/16 is the largest size that uses the NASM33537 "sizes .3125 and smaller" FP rule (sec 7), so FP is not Ln + 6P here. FB = Ln + 4P and H = Ln + 1P check out (P = .0556).
- Drill (S-HC Table V): Q (.3320), the same in aluminum and steel, inside the tapped-hole minor diameter range.
- Insert set-down (NASM33537 sec 11): .0417-.0833 with countersink (0.75P-1.5P), .0139-.0278 without (0.25P-0.5P).
- No nut. M45938/1's 5/16 nuts (dashes 21/22) are 5/16-24, so there is no mil 5/16-18 clinch nut; PEM makes commercial S/CLS/SP-0518 nuts (S-PEMCL), but they are not in the data. The app disables 5/16-18 for nuts and its warning explains why. Studs: see next section.

## 5/16-18 studs (v1.9.0, data v0.15; removed from the app in v0.18)
- Not offered in the app: the 12 rows have `in_app_scope` N and stay as a verified spec record, with their drill, stack and PEM performance rows.
- PNs (S-63540 Table II): M63540/1-51 to -56 (carbon steel 1005-1018, cadmium plated, 80 HRB sheet max) and -51C to -56C (CRES XM-7/303/303Se, passivated, 70 HRB sheet max). L .625/.750/.875/1.000/1.250/1.500 +/-.015. CAGE 80205 (legacy 81349), the same as the other studs. Carbon rows stay as spec record under the CRES-only rule.
- Dimensions: MIL-S-63540/1A Table I .313-18: head Ø.376 +/-.015, unthreaded S .150 max. Table IV as replaced by Amendment 2 (S-63540-A2): hole .311 +.003/-.000, min sheet .093, min edge distance .375, nut tightening torque 120 in-lb max. Table III (.093 sheet): push-out 400 lb / torque-out 140 in-lb in aluminum, 650 lb / 160 in-lb in cold-rolled steel.
- Commercial reference (S-PEMFH): FH-0518 / FHS-0518 length codes 10-24 match the mil dashes on thread, L, hole, head, min sheet and edge; PEM S max is .160 vs mil .150. FH-0518-8 (.500 long) has no mil dash and is not included. PEM performance rows (.093 aluminum and steel) were added to `pem_fh_performance.csv`.
- Drill (S-DRILL): 5/16 (.3125), inside the .311-.314 hole range. Letter drills N (.302) and O (.316) fall outside it.
- Stud stack: NAS1149C0563 washer (5/16, CRES, .063 +/-.006, ID .328, OD .562; S-NAS1149) and MS17830-5C 5/16-18 CRES lock nut (height .271-.360; S-MS17830-5). MS21044C5 is 5/16-24, so it is not used.
- No PEM FH4 is made in 5/16-18, so A304 sheet (above the 70 HRB CRES rating) has no rated stud; the app warns and recommends a helical insert.

## 5/16-24 inserts and nuts (v1.9.0, data v0.16)
- Only 5/16 thread in the app. An old link or state with 5/16-18 is moved to 5/16-24 with a notice (`pitchSwitch()` in `src/logic.js`).
- Inserts, PNs (S-KATO, S-HCXREF): 1D/1.5D/2D/2.5D/3D = MS124657/697/737/777/817 = MS21208F5-10/15/20/25/30 = Heli-Coil 1191-5CN312/469/625/781/938. CAGE 80205 / 96906.
- Insert dimensions: NASM33537 Table IV p9, .3125-24 column, checked against the page image. Ln .312/.469/.625/.781/.938; FP .718/.874/1.030/1.186/1.342; FB .479/.635/.791/.947/1.103; H .350/.510/.670/.820/.980; tapped-hole minor dia .3215 min, .3312 max (2B); countersink Ø.360-.390. Heli-Coil Tables V and VII give the same FP, FB, H, countersink and minor diameter. The sec 7 "sizes .3125 and smaller" FP rule applies.
- Insert drill (S-HC Table V): 21/64 (.3281) in aluminum and steel, inside the minor diameter range. Set-down .0312-.0625 with countersink, .0104-.0208 without (P = .0417).
- Nuts (S-45938 Table I/II page images): M45938/1-21/21C/21A (A .087, sheet .091-.124) and -22/22C/22A (A .120, sheet .125 up). Hole .413 +.003/-.000, C .411, E .500 +/-.015, T .234 +/-.015, min edge .375. Table II .3125: install 7000 lb; push-out 300 lb / torque-out 160 in-lb (carbon, CRES), 225 lb / 130 in-lb (aluminum).
- Nut PEM cross reference (S-PEMCL pg 4): S/CLS-0524-2 and -3 match on thread, A, hole and E; C .412, T .230 and edge .38 differ slightly. PEM CLA stops at 1/4-20, so the aluminum dashes have no PEM equivalent.
- Commercial-only SP-0524-1 (A .054, min sheet .056) and SP-0524-2 (A .087, min sheet .090) for A304 (S-PEMSS pg SS-3, SS-23: 470 lb / 170 in-lb and 750 lb / 250 in-lb averages in 304 stainless). No mil PN exists.
- Nut drill (S-DRILL): Z (.413), at the spec minimum of the .413-.416 hole.
- Studs: M63540/1 has no 5/16-24 stud, so 5/16-24 is disabled for studs; the warning offers 1/4-20.
