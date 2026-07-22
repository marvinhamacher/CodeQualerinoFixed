# 0. Inhaltsverzeichnis 










# 1 Verletzung SOLID, Testbarkeit, Architektur und Modellierung im Überblick


---
## 1.1 Schwere Verletzungen
| Kategorie               | Datei              | Klasse               | Grund                          | Beschreibung                                                                          | Wartbarkeitseinschränkung                        | Severity |
| ----------------------- | ------------------ | -------------------- | ------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------------ | ------ |
| Architektur / Schichten | `task_manager.py`  | `TaskManager`        | Direkte DB-Zugriffe            | Verletzt Schichtenarchitektur, Geschäftslogik ist an Infrastruktur gekoppelt.         | Sehr schlechte Testbarkeit und hohe Kopplung     | High  |
| SOLID (SRP)             | `task_manager.py`  | `TaskManager`        | Mehrere Verantwortlichkeiten   | Verantwortlich für Businesslogik, Datenbank, Benachrichtigung und Benutzerverwaltung. | Änderungen betreffen viele Bereiche gleichzeitig | High |
| SOLID (DIP)             | `task_manager.py`  | `TaskManager`        | Direkte Abhängigkeiten         | Arbeitet direkt mit `Database` und `EmailService`.                                    | Keine Austauschbarkeit, schwierige Tests         | High |
| Architektur             | `email_service.py` | `EmailService`       | Direkte externe Abhängigkeiten | SMTP wird direkt verwendet.                                                           | Schlechte Testbarkeit                            | High |
| UML                     | `notifications.py` | `NotificationCenter` | Hohe Kopplung                  | Direkte Abhängigkeit zu `EmailService`.                                               | Schlechte Erweiterbarkeit                        | High |
| SOLID (SRP)             | `database.py`      | `Database`           | Mehrere Verantwortlichkeiten   | Verwaltung von Tasks und Usern in einer Klasse.                                       | Erschwerte Wartung                               | High |
| Architektur             | `main.py`          | -                    | Fehlende Modularisierung       | Keine saubere Paketstruktur.                                                          | Schlechte Skalierbarkeit                         | High |
| TDD                     | mehrere Dateien    | -                    | Fehlende Unit Tests            | Keine Tests für Kernklassen.                                                          | Änderungen bergen hohes Risiko                   | High |
| TDD                     | `task_manager.py`  | `TaskManager`        | Viele Seiteneffekte            | `create_task()` führt mehrere Operationen gleichzeitig aus.                           | Schwer testbar                                   | High |
| Architektur             | `task_manager.py`  | `TaskManager`        | Fat Controller                 | Enthält zu viel Geschäftslogik.                                                       | Geringe Wartbarkeit                              | High |

---
## 1.2 Mittelschwerwiegende Verletzungen


| Kategorie   | Datei                 | Klasse                      | Grund                                      | Beschreibung                                             | Wartbarkeitseinschränkung  | Severity |
| ----------- | --------------------- | --------------------------- | ------------------------------------------ | -------------------------------------------------------- | -------------------------- | -------- |
| UML         | `report_generator.py` | `ReportGenerator`           | Direkte Database-Abhängigkeit              | Initialisiert Datenbank selbst.                          | Schlechte Testbarkeit      | Medium |
| UML         | `task_manager.py`     | `TaskManager`               | Direkte Notification-Abhängigkeit          | NotificationCenter wird direkt erzeugt.                  | Erschwerte Erweiterbarkeit | Medium |
| UML         | `task_manager.py`     | `TaskManager`               | Direkte EmailService-Abhängigkeit          | Direkte Initialisierung.                                 | Hohe Kopplung              | Medium |
| UML         | mehrere               | -                           | Fehlende Interfaces                        | Keine Abstraktionen für Datenbank oder Benachrichtigung. | Schlechte Austauschbarkeit | Medium |
| SOLID (OCP) | `report_generator.py` | `ReportGenerator`           | Nicht erweiterbar                          | Neue Berichtstypen benötigen Codeänderungen.             | Schlechte Erweiterbarkeit  | Medium |
| SOLID (ISP) | `email_service.py`    | `EmailService`              | Zu breite Schnittstelle                    | Clients benötigen nicht alle Funktionen.                 | Unnötige Abhängigkeiten    | Medium |
| SOLID (LSP) | `user_types.py`       | `AdminUser`, `ReadOnlyUser` | Basisklassenvertrag verletzt               | Unterklassen verändern erwartetes Verhalten.             | Unerwartete Fehler         | Medium |
| Architektur | `task_manager.py`     | `TaskManager`               | Repository Pattern fehlt                   | Direkter DB-Zugriff statt Repository.                    | Schlechte Testbarkeit      | Medium |
| Architektur | `report_generator.py` | `ReportGenerator`           | Separation of Concerns verletzt            | Formatierung und Businesslogik vermischt.                | Erschwerte Wartung         | Medium |
| TDD         | `report_generator.py` | `ReportGenerator`           | Fehlende Integrationstests                 | Berichte werden nicht validiert.                         | Fehler werden spät erkannt | Medium |
| TDD         | mehrere               | -                           | Business-Logik mit Infrastruktur vermischt | Schlechte Testbarkeit                                    | Wartungsaufwand steigt     | Medium |

---
## 1.3 Leichte Verletzungen

| Kategorie | Datei      | Klasse | Grund                   | Beschreibung                        | Wartbarkeitseinschränkung | Severity |
| --------- | ---------- | ------ | ----------------------- | ----------------------------------- | ------------------------- | ----- |
| TDD       | `utils.py` | -      | Fehlende Tests          | Utility-Funktionen sind ungetestet. | Erhöhtes Fehlerrisiko     | Low   |
| SOLID     | mehrere    | -      | Kleinere SOLID-Verstöße | Verbesserungspotenzial              | Begrenzter Einfluss       | Low |

---
## 1.4 Architektonische Empfehlungen

| Empfehlung        | Datei/Klasse                | Nutzen                                               | Priorität    |
| ----------------- | --------------------------- | ---------------------------------------------------- | ------------ |
| Strategy Pattern  | `NotificationCenter`        | Neue Benachrichtigungskanäle ohne `if/else` ergänzen | Empfehlung |
| Builder Pattern   | `TaskManager.create_task()` | Übersichtlichere Objekterzeugung                     | Empfehlung |
| Decorator Pattern | `EmailService`              | Logging/Retry flexibel ergänzen                      | Empfehlung |
| Singleton         | `Database`                  | Gemeinsame Datenbankinstanz                          | Empfehlung |
| Facade Pattern    | `TaskManager`               | Geringere Kopplung der Subsysteme                    | Empfehlung |
| Adapter Pattern   | `UserManager`               | Einfachere Integration externer Systeme              | Empfehlung |
| Command Pattern   | `TaskManager`               | Undo/Redo und bessere Nachvollziehbarkeit            | Empfehlung |






# 2.1 5 Solid Verletzungen im Detail:
## SOLID-Prinzipien

### Single Responsibility Principle bei `task_manager.py` verletzt

- **Grund:** Eine Klasse hat zu viele Verantwortlichkeiten gegenüber anderen Klassen.
- **Wartbarkeit:** Änderungen an einer Verantwortlichkeit können unbeabsichtigt Auswirkungen auf andere Funktionen haben. Die Klasse wird dadurch schwerer verständlich, schwieriger zu testen und aufwendiger zu erweitern.
- **Severity:** **High**, da der `TaskManager` eine zentrale Klasse ist und viele Komponenten von ihr abhängen.

---

### Single Responsibility Principle bei `database.py` verletzt

- **Grund:** `database.py` hat mehrere Verantwortlichkeiten: Laden und Speichern sowohl von Usern als auch von Tasks.
- **Wartbarkeit:** Änderungen an der Benutzerverwaltung können unbeabsichtigt die Task-Verwaltung beeinflussen und umgekehrt. Die Klasse wächst mit jeder neuen Funktion weiter an und wird zunehmend schwer wartbar.
- **Severity:** **High**, da die Datenbankklasse eine zentrale Komponente der Anwendung ist.

---

### Open/Closed Principle bei `report_generator.py` verletzt

- **Grund:** Für neue Berichtsarten muss die Klasse geändert werden.
- **Wartbarkeit:** Jede Erweiterung erfordert Änderungen am bestehenden Code. Dadurch steigt das Risiko, bereits funktionierende Berichte unbeabsichtigt zu beeinflussen oder Fehler einzuführen.
- **Severity:** **Medium**, da die Funktionalität eingeschränkt erweiterbar ist, die Kernfunktion der Anwendung jedoch weiterhin funktioniert.

---

### Liskov Substitution Principle bei `user_types.py` verletzt

- **Grund:** Unterklassen sind nicht immer kompatibel mit der Basisklasse.
- **Wartbarkeit:** Entwickler können sich nicht darauf verlassen, dass Unterklassen überall dort eingesetzt werden können, wo die Basisklasse erwartet wird. Das erschwert die Wiederverwendbarkeit und erhöht den Testaufwand.
- **Severity:** **Medium**, da fehlerhaftes Verhalten erst zur Laufzeit auftreten kann und die Klassenhierarchie schwerer verständlich wird.

---

### Interface Segregation Principle und Single Responsibility Principle bei `email_service.py` verletzt

#### Interface Segregation Principle (ISP)

- **Grund:** `report_generator.py` nutzt den `email_service.py` jedoch nur für `send`. Der `email_service.py` instanziiert jedoch alle Schnittstellen, egal ob SMS-Funktionen oder Push-Benachrichtigungen, und widerspricht somit der in den Vorlesungen aufgeführten Regel: *„Wenn ein Interface mehr Methoden hat, als ein Aufrufer braucht, entsteht eine unnötige Abhängigkeit.“*

- **Wartbarkeit:** Änderungen an SMS- oder Push-Funktionen können Auswirkungen auf Komponenten haben, die ausschließlich E-Mails versenden. Dadurch entstehen unnötige Abhängigkeiten und der Wartungsaufwand steigt.

#### Single Responsibility Principle (SRP)

- **Grund:** Die Klasse heißt `EmailService` und sollte sich deshalb nur um E-Mails kümmern, übernimmt allerdings auch den Versand von SMS und Push-Benachrichtigungen.

- **Wartbarkeit:** Die Klasse bündelt mehrere Verantwortlichkeiten. Änderungen an einer Benachrichtigungsart können andere Versandwege beeinflussen und erschweren das Verständnis sowie die Erweiterbarkeit der Klasse.

- **Severity:** **Medium**, da die Funktionalität zwar erhalten bleibt, die Klasse jedoch unnötig stark gekoppelt ist.

---

### Dependency Inversion Principle bei `task_manager.py` verletzt

- **Grund:** High-Level-Module hängen von Low-Level-Modulen ab. `TaskManager` initialisiert die Klassen `Database` und `NotificationCenter` direkt und bekommt sie nicht durch eine Abstraktion bereitgestellt.

- **Wartbarkeit:** Änderungen an `Database` oder `NotificationCenter` können direkte Anpassungen am `TaskManager` erforderlich machen. Außerdem wird das Testen erschwert, da Mock-Objekte oder alternative Implementierungen nicht einfach eingesetzt werden können.

- **Severity:** **High**, da die hohe Kopplung die Erweiterbarkeit und Testbarkeit der zentralen Geschäftslogik erheblich einschränkt.
