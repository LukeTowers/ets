# CLAUDE.md — Agent guide to the ACGP Electronic Timesheets codebase

This file orients AI agents working in this repository. It is a map, not a full spec — the
**authoritative documentation lives in `../docs/`** (a 13-document set that fully itemizes the
app's purpose, functionality, and known issues for a planned NativePHP/Laravel rebuild). Start
there for anything beyond a quick code change.

## What this app is

The **Air Cadets Gliding Program (ACGP) Electronic Timesheets** app — an iPad application used
by duty staff at Canadian Air Cadet gliding centres to record glider and towplane flight
operations, personnel attendance, qualifications, and to generate the official daily timesheet
and statistics reports. It runs **offline in the field** and syncs between iPads and HQ via
**CloudKit**. See [`../docs/01-overview.md`](../docs/01-overview.md).

- **Scale**: ~33,000 lines of Swift. **Status**: effectively frozen (last substantive work
  ~2022); currently receiving documentation/maintenance for a rebuild.
- **Version**: 6.2.8 (build 640). iOS targets: **iOS 13.4 / Swift 5**. tvOS: **tvOS 11 / Swift 4**.

## Build targets (see [`../docs/05-app-architecture.md`](../docs/05-app-architecture.md))

| Target | Kind | Role |
|---|---|---|
| `Timesheets` | iPad app | The main app (all data entry, reports) |
| `Flights Today` | tvOS app | Read-only dashboard |
| `Hours This Season`, `Flights This Season`, `Gliding Centre Flights` | Today widgets | Home-screen glances |
| `TimesheetsTests`, `TimesheetsUITests`, `Flights TodayTests`, `Flights TodayUITests` | Test bundles | |

App group: `group.ca.cadets.Timesheets`. CloudKit container: `iCloud.ca.cadets.Timesheets`.
CI: `.github/workflows/swift.yml` (builds the iOS app only; **does not build tvOS**).

## Code layout

- `Core Classes/` — the heart. `DataModel.swift` (the `dataModel` god object),
  `CloudKitController.swift` (3.8k lines of sync), `CoreDataController.swift`,
  `ReportGenerator.swift` + `Reports/`, `DeduplicationManager.swift`, `UserDefaults.swift`
  (all settings), `iBeaconManager.swift`, `GPSmanager.swift`.
  - `Core Classes/3 Main View Controllers/` — the three areas: `Pilots.swift` (Attendance),
    `Airplanes.swift` (Aircraft), `Records.swift` (Timesheets/Records).
- `CoreData/` — the 15 `NSManagedObject` subclasses with business logic (`Pilot.swift`,
  `AircraftTimesheet.swift`, `FlightRecord.swift`, `AttendanceRecord.swift`, …). Plus
  `MedicalImage.swift` at repo root.
- `Model.xcdatamodeld/` — Core Data model; current version is **`Model 21.xcdatamodel`**.
- `Pilot Classes/`, `Aircraft Classes/`, `Record Classes/`, `Main View Classes/` — screens/popovers.
- `Categories/` — Swift extensions + globals. `GlobalFunctionsAndDeclarations.swift` and
  `GlobalConstants.swift` hold the enums (VehicleType, quals, sequences) and NotificationCenter
  names.
- `plists/` — **business rules as data**: `GliderList.plist` (fleet), `TowplaneSequences.plist` /
  `GliderSequences.plist` (flight-type rules), `GlidingCentreCoordinates.plist` (sites),
  `RGSflights.plist`, `OrganizeTemplate.plist`.
- `Flights Today/` — tvOS read side. `Resources/` — sample sqlite DBs + per-aircraft `.aiff`
  landing sounds.

## Key concepts before you touch anything

- **Record identity is a creation timestamp** (`recordID: Date` → CloudKit recordName = epoch
  string). Aircraft use a natural key (region+registration). This is fragile by design; see
  [`../docs/04-sync-architecture.md`](../docs/04-sync-architecture.md).
- **Sentinels**: `AttendanceRecord.timeOut == Date.distantFuture` means "still signed in";
  `FlightRecord.timeDown == Date.distantFuture` means "airborne"; qual dates of `distantPast`
  mean "never".
- **Conflict resolution** is whole-record last-writer-wins on `recordChangeTime` (device wall
  clock). `shouldUpdateChangeTimes`, `observerMode`, `trainingMode` are global gates.
- **Threading**: Core Data is main-queue-only here.
- **Enums are often stringly-typed** (participant types, sequences, qual names).

## Working conventions

- The project uses **Xcode** (`Timesheets.xcodeproj`); there is no SPM/CocoaPods manifest — all
  dependencies are vendored (`Reachability.swift`, `NDHTMLtoPDF.swift`).
- Build/test via `xcodebuild` against the `Timesheets` scheme, or the CI workflow as reference.
- **Do not add new features here.** This codebase is in maintenance/documentation mode ahead of
  a rebuild. Bug fixes and doc updates are in scope; net-new functionality should target the
  rebuild.
- When you discover a behavior that looks like a bug, check
  [`../docs/11-issues-and-tech-debt.md`](../docs/11-issues-and-tech-debt.md) — it may already be
  cataloged (ISSUE-0xx) with a verdict on bug-vs-intent.
- Governance: `CONTRIBUTING.md`, `SECURITY.md` (do not file security issues publicly),
  `CODEOFCONDUCT.md`. License: MIT, © Government of Canada. Repo docs are bilingual (EN/FR)
  though the **app UI is English-only**.

## The rebuild docs (`../docs/`)

1. Overview · 2. Domain Glossary · 3. Data Model · 4. Sync Architecture · 5. App Architecture ·
6. Attendance & Personnel · 7. Flight Operations · 8. Records & Editing · 9. Reports Reference ·
10. Stats & Peripherals · 11. Issues & Tech Debt · 12. NativePHP Migration Blueprint ·
13. Open Questions (awaiting answers from active field users).
