# 0. Inhaltsverzeichnis 










# 1 Verletzung SOLID, Testbarkeit, Architektur und Modellierung im Überblick



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

## 1.3 Leichte Verletzungen

| Kategorie | Datei      | Klasse | Grund                   | Beschreibung                        | Wartbarkeitseinschränkung | Severity |
| --------- | ---------- | ------ | ----------------------- | ----------------------------------- | ------------------------- | ----- |
| TDD       | `utils.py` | -      | Fehlende Tests          | Utility-Funktionen sind ungetestet. | Erhöhtes Fehlerrisiko     | Low   |
| UML       | mehrere    | -      | Kleine Strukturprobleme | Geringe UML-Unsauberkeiten          | Kaum Auswirkungen         | Low  |
| SOLID     | mehrere    | -      | Kleinere SOLID-Verstöße | Verbesserungspotenzial              | Begrenzter Einfluss       | Low |

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


