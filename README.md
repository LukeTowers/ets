![](https://github.com/cadets-ca/ets/workflows/CI/badge.svg)

# ACGP ETS

The Air Cadets Gliding Program Electronic Timesheets mobile application.

ETS is the iPad app used by duty staff at Canadian Air Cadet gliding centres to record glider
and towplane flight operations, track personnel attendance and qualifications, and generate the
official daily timesheet and statistics reports. It is built to run **offline in the field** and
synchronises between iPads and headquarters via **CloudKit**.

## FTÉ PVVCA _tentativement_

L'application mobile de Feuilles de temps électroniques pour le programme de vol à voile des
cadets de l'air.

---

## Documentation

- **Rebuild documentation set → [`../docs/`](../docs/)** — a comprehensive, code-traceable spec
  of the app's purpose, functionality, business rules, and known issues, written to support a
  planned **NativePHP / Laravel** rebuild. Start with [`../docs/README.md`](../docs/README.md).
- **Agent guide → [`CLAUDE.md`](CLAUDE.md)** — a map of this codebase for AI agents and new
  contributors.
- **User guide → [`../ets-docs/`](../ets-docs/)** — the published end-user guide (Attendance,
  Aircraft, Timesheets) and iPad setup checklist.

## Project status

Version 6.2.8 (build 640). The codebase (~33k lines of Swift; iOS 13.4 / Swift 5, plus a tvOS
companion and three Today widgets) is in **maintenance and documentation mode** ahead of a
cross-platform rebuild. New features should target the rebuild, not this repository.

## Building

Open `Timesheets.xcodeproj` in Xcode and build the **Timesheets** scheme. Dependencies are
vendored (no SPM/CocoaPods). See `.github/workflows/swift.yml` for the CI build, and
[`CLAUDE.md`](CLAUDE.md) for a tour of the code layout and key architectural concepts.

## Contributing & governance

See [`CONTRIBUTING.md`](CONTRIBUTING.md), [`CODEOFCONDUCT.md`](CODEOFCONDUCT.md), and
[`SECURITY.md`](SECURITY.md). **Do not file security issues on the public repository.**
Licensed under the [MIT License](LICENSE). Repository documentation is bilingual (EN/FR); the
app UI is currently English-only.
