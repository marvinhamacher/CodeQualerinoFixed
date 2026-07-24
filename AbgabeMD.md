# 0. Inhaltsverzeichnis 










# 1.1 Verletzung SOLID, Testbarkeit, Architektur und Modellierung im Überblick


---
## 1.1.1 Schwere Verletzungen
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
## 1.1.2 Mittelschwerwiegende Verletzungen


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
## 1.1.3 Leichte Verletzungen

| Kategorie | Datei      | Klasse | Grund                   | Beschreibung                        | Wartbarkeitseinschränkung | Severity |
| --------- | ---------- | ------ | ----------------------- | ----------------------------------- | ------------------------- | ----- |
| TDD       | `utils.py` | -      | Fehlende Tests          | Utility-Funktionen sind ungetestet. | Erhöhtes Fehlerrisiko     | Low   |
| SOLID     | mehrere    | -      | Kleinere SOLID-Verstöße | Verbesserungspotenzial              | Begrenzter Einfluss       | Low |

---
## 1.1.4 Architektonische Empfehlungen

| Empfehlung        | Datei/Klasse                | Nutzen                                               | Priorität    |
| ----------------- | --------------------------- | ---------------------------------------------------- | ------------ |
| Strategy Pattern  | `NotificationCenter`        | Neue Benachrichtigungskanäle ohne `if/else` ergänzen | Empfehlung |
| Builder Pattern   | `TaskManager.create_task()` | Übersichtlichere Objekterzeugung                     | Empfehlung |
| Decorator Pattern | `EmailService`              | Logging/Retry flexibel ergänzen                      | Empfehlung |
| Singleton         | `Database`                  | Gemeinsame Datenbankinstanz                          | Empfehlung |
| Facade Pattern    | `TaskManager`               | Geringere Kopplung der Subsysteme                    | Empfehlung |
| Adapter Pattern   | `UserManager`               | Einfachere Integration externer Systeme              | Empfehlung |
| Command Pattern   | `TaskManager`               | Undo/Redo und bessere Nachvollziehbarkeit            | Empfehlung |






## 1.2 Fünf SOLID Verletzungen im Detail:
### 1.2.1 Single Responsibility Principle bei `task_manager.py` verletzt

- **Grund:** Eine Klasse hat zu viele Verantwortlichkeiten gegenüber anderen Klassen.
- **Wartbarkeit:** Änderungen an einer Verantwortlichkeit können unbeabsichtigt Auswirkungen auf andere Funktionen haben. Die Klasse wird dadurch schwerer verständlich, schwieriger zu testen und aufwendiger zu erweitern.
- **Severity:** **High**, da der `TaskManager` eine zentrale Klasse ist und viele Komponenten von ihr abhängen.

---

### 1.2.2 Single Responsibility Principle bei `database.py` verletzt

- **Grund:** `database.py` hat mehrere Verantwortlichkeiten: Laden und Speichern sowohl von Usern als auch von Tasks.
- **Wartbarkeit:** Änderungen an der Benutzerverwaltung können unbeabsichtigt die Task-Verwaltung beeinflussen und umgekehrt. Die Klasse wächst mit jeder neuen Funktion weiter an und wird zunehmend schwer wartbar.
- **Severity:** **High**, da die Datenbankklasse eine zentrale Komponente der Anwendung ist.

---

### 1.2.3 Open/Closed Principle bei `report_generator.py` verletzt

- **Grund:** Für neue Berichtsarten muss die Klasse geändert werden.
- **Wartbarkeit:** Jede Erweiterung erfordert Änderungen am bestehenden Code. Dadurch steigt das Risiko, bereits funktionierende Berichte unbeabsichtigt zu beeinflussen oder Fehler einzuführen.
- **Severity:** **Medium**, da die Funktionalität eingeschränkt erweiterbar ist, die Kernfunktion der Anwendung jedoch weiterhin funktioniert.

---

### 1.2.3 Liskov Substitution Principle bei `user_types.py` verletzt

- **Grund:** Unterklassen sind nicht immer kompatibel mit der Basisklasse.
- **Wartbarkeit:** Entwickler können sich nicht darauf verlassen, dass Unterklassen überall dort eingesetzt werden können, wo die Basisklasse erwartet wird. Das erschwert die Wiederverwendbarkeit und erhöht den Testaufwand.
- **Severity:** **Medium**, da fehlerhaftes Verhalten erst zur Laufzeit auftreten kann und die Klassenhierarchie schwerer verständlich wird.

---

###  1.2.4 Interface Segregation Principle und Single Responsibility Principle bei `email_service.py` verletzt

#### Interface Segregation Principle (ISP)

- **Grund:** `report_generator.py` nutzt den `email_service.py` jedoch nur für `send`. Der `email_service.py` instanziiert jedoch alle Schnittstellen, egal ob SMS-Funktionen oder Push-Benachrichtigungen, und widerspricht somit der in den Vorlesungen aufgeführten Regel: *„Wenn ein Interface mehr Methoden hat, als ein Aufrufer braucht, entsteht eine unnötige Abhängigkeit.“*

- **Wartbarkeit:** Änderungen an SMS- oder Push-Funktionen können Auswirkungen auf Komponenten haben, die ausschließlich E-Mails versenden. Dadurch entstehen unnötige Abhängigkeiten und der Wartungsaufwand steigt.

#### Single Responsibility Principle (SRP)

- **Grund:** Die Klasse heißt `EmailService` und sollte sich deshalb nur um E-Mails kümmern, übernimmt allerdings auch den Versand von SMS und Push-Benachrichtigungen.

- **Wartbarkeit:** Die Klasse bündelt mehrere Verantwortlichkeiten. Änderungen an einer Benachrichtigungsart können andere Versandwege beeinflussen und erschweren das Verständnis sowie die Erweiterbarkeit der Klasse.

- **Severity:** **Medium**, da die Funktionalität zwar erhalten bleibt, die Klasse jedoch unnötig stark gekoppelt ist.

---

###  1.2.5 Dependency Inversion Principle bei `task_manager.py` verletzt

- **Grund:** High-Level-Module hängen von Low-Level-Modulen ab. `TaskManager` initialisiert die Klassen `Database` und `NotificationCenter` direkt und bekommt sie nicht durch eine Abstraktion bereitgestellt.

- **Wartbarkeit:** Änderungen an `Database` oder `NotificationCenter` können direkte Anpassungen am `TaskManager` erforderlich machen. Außerdem wird das Testen erschwert, da Mock-Objekte oder alternative Implementierungen nicht einfach eingesetzt werden können.

- **Severity:** **High**, da die hohe Kopplung die Erweiterbarkeit und Testbarkeit der zentralen Geschäftslogik erheblich einschränkt.

# 3 Refactoring

## 3.1 Behebung der 5 SOLID - Verletzung

Oben wurden sich 5 SOLID - Verletzungen im Detail angesehen.
Folgend werden 5 Verletzungen mit Sourcecode behoben. 
Im Abteil 3.2 lassen sich die ADRs für das Refactoring finden.

### 3.1.1  Single Responsibility Principle bei `task_manager.py`
`task_manager.py` hat aktuell Methoden, die nicht nur für das Verwalten von Aufgaben wichtig sind. 
Beispielsweise besitzt die Datei die Methode set_reminder und find_overdue welche die Logik zum Ermitteln überfälliger Aufgaben sowie das Versenden von Erinnerungen übernehmen. 
Diese Verantwortlichkeiten gehören jedoch nicht zur eigentlichen Aufgabenverwaltung.
Auch die Methode format_task kann ausgelagert werden da sie keine CRUD-relevante Aufgabe übernimmt und nur von der Methode Main aufgerufen wird. 


## Vorher 

# SCHICHT: Infrastruktur — Persistenz

```python
import json
import os
from logger import log, log_error
from config import DB_FILE, USER_FILE


class Database:
    def __init__(self):
        self.d = {}
        self.u = {}
        os.makedirs(os.path.dirname(DB_FILE), exist_ok=True)
        self.load()

    def load(self):
        if os.path.exists(DB_FILE):
            f = open(DB_FILE, "r")
            try:
                self.d = json.loads(f.read())
            except:
                log_error("Konnte tasks.json nicht laden")
                self.d = {}
            f.close()
        else:
            self.d = {}

        if os.path.exists(USER_FILE):
            f = open(USER_FILE, "r")
            try:
                self.u = json.loads(f.read())
            except:
                log_error("Konnte users.json nicht laden")
                self.u = {}
            f.close()
        else:
            self.u = {}

    def save(self):
        f = open(DB_FILE, "w")
        f.write(json.dumps(self.d))
        f.close()
        f = open(USER_FILE, "w")
        f.write(json.dumps(self.u))
        f.close()
        log("Gespeichert")

    # Tasks
    def get_task(self, tid):
        if str(tid) in self.d:
            return self.d[str(tid)]
        return None

    def save_task(self, tid, task):
        if task.get("title") is None or task.get("title") == "":
            log_error("Task ohne Titel kann nicht gespeichert werden")
            return False
        if task.get("priority", 0) < 1 or task.get("priority", 0) > 3:
            log_error("Ungueltige Prioritaet")
            return False
        self.d[str(tid)] = task
        self.save()
        return True

    def delete_task(self, tid):
        if str(tid) in self.d:
            del self.d[str(tid)]
            self.save()

    def all_tasks(self):
        return self.d

    # Users
    def get_user(self, uid):
        if str(uid) in self.u:
            return self.u[str(uid)]
        return None

    def save_user(self, uid, user):
        if user.get("name") is None or user.get("name") == "":
            log_error("User ohne Name kann nicht gespeichert werden")
            return False
        self.u[str(uid)] = user
        self.save()
        return True

    def delete_user(self, uid):
        if str(uid) in self.u:
            del self.u[str(uid)]
            self.save()

    def all_users(self):
        return self.u
```
## Nachher

#### task_manager.py

```python
from datetime import datetime
from database import Database
from user import UserManager
from logger import log_error, log_info, log_warning
from reminder_service import ReminderService
from task_formatter import TaskFormatter

class TaskManager:

    def __init__(self):
        self.database = Database()
        self.user_manager = UserManager()
        self.reminder_service = ReminderService(self.database, self.user_manager)
        self.task_formatter = TaskFormatter()
        self.created_task_count = 0

    def create_task(self, task_id, title, description, priority, assignee_id, due=None):
        if not title:
            log_error("Titel darf nicht leer sein")
            return False

        if priority not in (1, 2, 3):
            log_error("Prioritaet muss zwischen 1 und 3 liegen")
            return False

        task = {
            "id": task_id,
            "title": title,
            "desc": description,
            "priority": priority,
            "status": "new",
            "assignee": assignee_id,
            "created": str(datetime.now()),
            "due": due,
        }

        if not self.database.save_task(task_id, task):
            return False

        self.created_task_count += 1
        log_info(f"Task {task_id} erstellt (Anzahl: {self.created_task_count})")
        return True

    def update_status(self, task_id, new_status):
        task = self.database.get_task(task_id)

        if task is None:
            log_error("Task nicht gefunden")
            return False

        if new_status not in ["new", "in_progress", "done", "cancelled"]:
            log_error("Unbekannter Status")
            return False

        old_status = task["status"]
        task["status"] = new_status
        self.database.save_task(task_id, task)

        log_info(f"Task {task_id}: {old_status} -> {new_status}")
        return True

    def delete_task(self, task_id):
        if self.database.get_task(task_id) is None:
            return False

        self.database.delete_task(task_id)
        log_warning(f"Task {task_id} geloescht")
        return True

    def get_task(self, task_id):
        return self.database.get_task(task_id)

    def all_tasks(self):
        return self.database.all_tasks()
```

#### reminder_service.py

```python
from datetime import datetime
from notifications import NotificationCenter

class ReminderService:

    def __init__(self, database, user_manager):
        self.database = database
        self.user_manager = user_manager
        self.notification_center = NotificationCenter()

    def find_overdue(self):
        overdue_tasks = []

        for task in self.database.all_tasks().values():
            if task.get("due") is None:
                continue

            if task["status"] in ["done", "cancelled"]:
                continue

            try:
                due_date = datetime.fromisoformat(task["due"])
            except ValueError:
                continue

            if due_date < datetime.now():
                overdue_tasks.append(task)

        return overdue_tasks

    def send_reminders(self):
        for task in self.find_overdue():
            user = self.user_manager.get_user(task["assignee"])
            if user is None:
                continue

            self.notification_center.notify(
                user,
                "email",
                "Aufgabe überfällig",
                f"Die Aufgabe '{task['title']}' ist überfällig."
            )
```

#### task_formatter.py

```python
class TaskFormatter:

    def format_task(self, task):
        if task is None:
            return "??"

        priority_names = {
            1: "niedrig",
            2: "mittel",
            3: "hoch",
        }

        return (
            f"#{task['id']} "
            f"{task['title']} "
            f"[{priority_names.get(task['priority'], 'unbekannt')}] "
            f"({task['status']})"
        )
```
### 3.1.2 Single Responsibility Principle bei `database.py` verletzt
`database.py` übernimmt derzeit die Persistenz sowohl für Aufgaben als auch für Benutzer. Die Klasse `Database` enthält dadurch die Methoden `get_task()`, `save_task()`, `delete_task()`, `all_tasks()` sowie `get_user()`, `save_user()`, `delete_user()` und `all_users()`. Änderungen an der Speicherung von Aufgaben oder Benutzern führen somit beide zu Änderungen an derselben Klasse.

Darüber hinaus ist die Implementierung nicht generisch aufgebaut, sondern speziell auf die Entitäten Task und User zugeschnitten. Dadurch lässt sich die Klasse nur schwer erweitern, beispielsweise wenn zukünftig weitere Entitäten gespeichert werden sollen.

Um das Single Responsibility Principle (SRP) einzuhalten, sollte die allgemeine Persistenzlogik in eine abstrakte Basisklasse `Database` ausgelagert werden. Diese stellt lediglich die grundlegenden Operationen

- `load()`
- `save()`
- `delete()`
- `get()`
- `get_all()`

als abstrakte Methoden bereit.

Anschließend können spezialisierte Klassen wie `TaskRepository` und `UserRepository` von dieser Basisklasse erben und die Methoden entsprechend ihrer jeweiligen Entität implementieren. Dadurch besitzt jede Repository-Klasse nur noch die Verantwortung für genau eine Datenbankentität, während die abstrakte Klasse lediglich die gemeinsame Schnittstelle definiert.


## 3.2 Einsatz von mehreren Mustern (3)


## 3.3 ADRs für Refactoring anhand SOLID-korrektur und Patterns


## 3.4 Neue Features (3)









