# Implementation Progress Report: FR01 Links to Manual (`manualPath`)

## Status Overview

| Nr. | Schritt                               | Status    | Hinweise / Kurzbeschreibung                                                      |
|-----|---------------------------------------|-----------|----------------------------------------------------------------------------------|
| 1   | **Scope & Constraints**               | ✅        | Scope, Out-of-Scope und File/Line-Limits klar definiert und dokumentiert         |
| 2   | **Backend: Data Model & DTO**         | ✅        | `manualPath` zu Entity und DTO hinzugefügt, Mapping/Adapter aktualisiert         |
| 3   | **Backend: Validation**               | ✅        | Regex und Normalisierung im Adapter umgesetzt; bei voller URL wird nur der Pfad gespeichert |
| 4   | **Backend: Persistence**              | ✅        | MongoDB-Mapping und -Abruf für `manualPath` gesichert                            |
| 5   | **Backend: API**                      | ✅        | `manualPath` in GET/PUT-Endpunkten, OpenAPI aktualisiert                         |
| 6   | **Backend: Testing**                  | ✅        | Regressiontests am 22.05.2025 durchgeführt: Alle bestehenden Unit- und Integrationstests erfolgreich, keine Anpassungen notwendig |
| 7   | **Frontend**                          | 🟡        | UI-Input, Validierung (akzeptiert volle URL oder Pfad, erlaubt Anker etc.), Tooltip, Anzeige, Tests; Validierungslogik und Anzeige mit Backend abgestimmt |
| 8   | **Dokumentation**                     | 🟡        | API-Doku, User Guidance, Peer-Review-Checkliste und technische Doku aktualisiert, finale Review/Abgleich mit Implementierung steht noch aus |
| 9   | **Verifikation & Troubleshooting**    | 🟡        | Cross-Layer-Checkliste und Troubleshooting: Konzept dokumentiert, Umsetzung/Abgleich mit Implementierung noch offen |
| 10  | **Lessons Learned**                   | ❌        |                                                                                  |

---

**Legende:**  
✅ Erledigt  🟡 Offen/Inkrementell  ❌ Nicht begonnen

**Letztes Update:** 22.05.2025

**Hinweis:**
Am 22.05.2025 wurden alle Unit- und Integrationstests nach Backend-Änderungen ausgeführt (siehe Terminal-Log). Ergebnis: Alle Tests erfolgreich, keine Anpassungen an bestehenden Tests erforderlich. Backend-Implementierung ist regressionsfrei.

**Zusatz:**

- Bei Eingabe einer vollen URL wird im Backend nur der Pfad gespeichert (siehe Mapping/Adapter und technische Doku).

- Frontend validiert und akzeptiert sowohl volle URL (mit Basis) als auch Pfad, erlaubt Anker (`#`), Slashes, Unterstriche, Bindestriche, Punkte, Groß-/Kleinschreibung.

- Validierungs- und Anzeige-Logik zwischen Frontend und Backend abgestimmt, Backend prüft sicherheitsrelevante Aspekte, Frontend kann strikter sein für bessere UX.