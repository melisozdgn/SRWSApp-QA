# Functional Test Cases — Navigation, Guide, Settings, Localization

Scope: splash, bottom navigation, Guide, Settings, and EN/TR localization. Language persistence across an app restart is not a defined requirement (the language is kept only in in-memory state); this will be covered as an observational test case in Phase 5.

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-NAV-001](#tc-nav-001) | Splash screen leads to Home | Medium | REQ-001 | E2E / Manual | integration_test |
| [TC-NAV-002](#tc-nav-002) | Bottom navigation switches between four tabs | High | REQ-007, REQ-013, REQ-014 | Widget / E2E | Widget test + integration_test |
| [TC-GUIDE-001](#tc-guide-001) | Guide lists all waste categories | Medium | REQ-013 | Widget | Widget test |
| [TC-GUIDE-002](#tc-guide-002) | Guide category detail shows fact and instructions | Medium | REQ-013 | Widget | Widget test |
| [TC-SET-001](#tc-set-001) | Settings lists expected items | Medium | REQ-014 | Widget | Widget test |
| [TC-SET-002](#tc-set-002) | Switching language EN → TR applies immediately | High | REQ-011 | Widget / E2E | Widget test + integration_test |
| [TC-SET-003](#tc-set-003) | Switching language TR → EN applies immediately | Medium | REQ-011 | Widget / E2E | Widget test |
| [TC-SET-004](#tc-set-004) | Info sub-screens open and close | Low | REQ-014 | Widget | Widget test |
| [TC-SET-005](#tc-set-005) | Reminders switch toggles its visual state | Low | REQ-014 | Widget | Widget test |
| [TC-LOC-001](#tc-loc-001) | Guide and Home screens are fully localized in Turkish | Medium | REQ-011 | Widget | Widget test (locale tr) |
| [TC-LOC-002](#tc-loc-002) | History screen is localized in Turkish | High | REQ-011 | Widget | Widget test (locale tr) |

## TC-NAV-001

| Field | Value |
|---|---|
| **Title** | Splash screen leads to Home |
| **Priority** | Medium |
| **Requirement** | REQ-001 |
| **Level / Automation** | E2E / Manual / integration_test |
| **Precondition** | App closed. |
| **Test Steps** | 1. Launch the app. |
| **Test Data** | — |
| **Expected Result** | • Splash is shown, then after ~3 s the main layout appears<br>• The default tab is **Sorting** (Home) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAV-002

| Field | Value |
|---|---|
| **Title** | Bottom navigation switches between four tabs |
| **Priority** | High |
| **Requirement** | REQ-007, REQ-013, REQ-014 |
| **Level / Automation** | Widget / E2E / Widget test + integration_test |
| **Precondition** | Main layout is open. |
| **Test Steps** | 1. Tap History, Sorting, Guide, Settings in turn. |
| **Test Data** | — |
| **Expected Result** | • Each tap shows the corresponding screen; tab labels are `History / Sorting / Guide / Settings` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-GUIDE-001

| Field | Value |
|---|---|
| **Title** | Guide lists all waste categories |
| **Priority** | Medium |
| **Requirement** | REQ-013 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | Guide tab is open. |
| **Test Steps** | 1. Inspect the Guide screen. |
| **Test Data** | — |
| **Expected Result** | • 7 categories are listed: Plastic, Paper & Cardboard, Glass, Metal, Batteries, E-Waste, Organic Waste<br>• The header shows "Recycling Guide" |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-GUIDE-002

| Field | Value |
|---|---|
| **Title** | Guide category detail shows fact and instructions |
| **Priority** | Medium |
| **Requirement** | REQ-013 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | Guide tab is open. |
| **Test Steps** | 1. Tap **Plastic**. |
| **Test Data** | `plasticFact`, `plasticInst1-4` (`app_en.arb`) |
| **Expected Result** | • The detail screen opens<br>• The info note ("Did you know?") and 4 instructions match `app_en.arb` text<br>• Back navigation returns to the Guide list |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SET-001

| Field | Value |
|---|---|
| **Title** | Settings lists expected items |
| **Priority** | Medium |
| **Requirement** | REQ-014 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | Settings tab is open. |
| **Test Steps** | 1. Inspect the Settings screen. |
| **Test Data** | — |
| **Expected Result** | • Visible items: Reminders/Daily sorting reminders toggle, Language, About SRWS (`Version 1.0.0`), Sustainability Goals, How to Use App, Privacy Policy |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SET-002

| Field | Value |
|---|---|
| **Title** | Switching language EN → TR applies immediately |
| **Priority** | High |
| **Requirement** | REQ-011 |
| **Level / Automation** | Widget / E2E / Widget test + integration_test |
| **Precondition** | App is in English. |
| **Test Steps** | 1. Settings → Language → select Turkish.<br>2. Navigate between tabs (Sorting, Guide, Settings). |
| **Test Data** | `app_tr.arb` |
| **Expected Result** | • No restart required<br>• Bottom-nav labels and Settings/Guide/Home text revert to `app_tr.arb` values |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SET-003

| Field | Value |
|---|---|
| **Title** | Switching language TR → EN applies immediately |
| **Priority** | Medium |
| **Requirement** | REQ-011 |
| **Level / Automation** | Widget / E2E / Widget test |
| **Precondition** | App is in Turkish. |
| **Test Steps** | 1. Settings → Language → select English. |
| **Test Data** | `app_en.arb` |
| **Expected Result** | • All localized text reverts to English; no restart required |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SET-004

| Field | Value |
|---|---|
| **Title** | Info sub-screens open and close |
| **Priority** | Low |
| **Requirement** | REQ-014 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | Settings is open. |
| **Test Steps** | 1. Open About SRWS, Sustainability Goals, How to Use App, Privacy Policy in turn and navigate back. |
| **Test Data** | — |
| **Expected Result** | • Each sub-screen opens with content; back navigation returns to Settings; no crash |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SET-005

| Field | Value |
|---|---|
| **Title** | Reminders switch toggles its visual state |
| **Priority** | Low |
| **Requirement** | REQ-014 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | Settings is open. |
| **Test Steps** | 1. Toggle the Daily sorting reminders switch on/off. |
| **Test Data** | — |
| **Expected Result** | • The switch's visual state changes<br>• (Scope note) Actual notification delivery is out of scope |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | There appears to be no functional notification wiring behind the switch; only its UI state is tested. |

## TC-LOC-001

| Field | Value |
|---|---|
| **Title** | Guide and Home screens are fully localized in Turkish |
| **Priority** | Medium |
| **Requirement** | REQ-011 |
| **Level / Automation** | Widget / Widget test (locale tr) |
| **Precondition** | App is in Turkish. |
| **Test Steps** | 1. Check every ARB-backed text on the Home, Guide and Settings screens. |
| **Test Data** | `app_tr.arb` |
| **Expected Result** | • No leftover hardcoded English text |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-LOC-002

| Field | Value |
|---|---|
| **Title** | History screen is localized in Turkish |
| **Priority** | High |
| **Requirement** | REQ-011 |
| **Level / Automation** | Widget / Widget test (locale tr) |
| **Precondition** | App is in Turkish; at least 1 record exists. |
| **Test Steps** | 1. Open the History screen.<br>2. Check the title, stat labels, rank, empty state, and dialog text. |
| **Test Data** | `yourDeviceLog`, `localImpact`, `totalScans`, `recentScans`, `clearHistory`… |
| **Expected Result** | • All text is shown using its `app_tr.arb` counterpart |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | **F-07:** these strings are hardcoded English in the code; this test may fail, in which case a bug is filed. |
