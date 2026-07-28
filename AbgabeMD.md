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

### 1.2.4 Liskov Substitution Principle bei `user_types.py` verletzt

- **Grund:** Unterklassen sind nicht immer kompatibel mit der Basisklasse.
- **Wartbarkeit:** Entwickler können sich nicht darauf verlassen, dass Unterklassen überall dort eingesetzt werden können, wo die Basisklasse erwartet wird. Das erschwert die Wiederverwendbarkeit und erhöht den Testaufwand.
- **Severity:** **Medium**, da fehlerhaftes Verhalten erst zur Laufzeit auftreten kann und die Klassenhierarchie schwerer verständlich wird.

---

###  1.2.5 Dependency Inversion Principle bei `task_manager.py` verletzt

- **Grund:** High-Level-Module hängen von Low-Level-Modulen ab. `TaskManager` initialisiert die Klassen `Database` und `NotificationCenter` direkt und bekommt sie nicht durch eine Abstraktion bereitgestellt.

- **Wartbarkeit:** Änderungen an `Database` oder `NotificationCenter` können direkte Anpassungen am `TaskManager` erforderlich machen. Außerdem wird das Testen erschwert, da Mock-Objekte oder alternative Implementierungen nicht einfach eingesetzt werden können.

- **Severity:** **High**, da die hohe Kopplung die Erweiterbarkeit und Testbarkeit der zentralen Geschäftslogik erheblich einschränkt.

# 3 Refactoring

## 3.1 Behebung der 5 SOLID - Verletzung

Oben wurden sich 5 SOLID - Verletzungen im Detail angesehen.
Folgend werden 5 Verletzungen mit Sourcecode behoben. 
Die Fixes bestehen in sich selber. 
Sie betrachten einzelne Fixes für die jeweiligen Klassen und nicht eine Klasse wo alle fehler auf ein mal behoben werden.
Die einzelbetrachtung sollen zeigen, dass die ursache des Problems verstanden wurde und auch verstanden wurde wie diese zu beheben ist.

### 3.1.1  Single Responsibility Principle bei `task_manager.py`
`task_manager.py` hat aktuell Methoden, die nicht nur für das Verwalten von Aufgaben wichtig sind. 
Beispielsweise besitzt die Datei die Methode set_reminder und find_overdue welche die Logik zum Ermitteln überfälliger Aufgaben sowie das Versenden von Erinnerungen übernehmen. 
Diese Verantwortlichkeiten gehören jedoch nicht zur eigentlichen Aufgabenverwaltung.
Auch die Methode format_task kann ausgelagert werden da sie keine CRUD-relevante Aufgabe übernimmt und nur von der Methode Main aufgerufen wird. 


### Vorher
```python
from datetime import datetime, timedelta
from database import Database
from email_service import EmailService
from notifications import NotificationCenter
from user import UserManager
from logger import log, log_error, log_info, log_warning


class TaskManager:
    def __init__(self):
        self.db = Database()
        self.email = EmailService()
        self.notif = NotificationCenter()
        self.users = UserManager()
        self.cnt = 0

    def create_task(self, tid, title, desc, prio, assignee_id, due=None, mode=1):
        if title is None or title == "":
            log_error("Titel darf nicht leer sein")
            return False
        if prio < 1 or prio > 3:
            log_error("Prioritaet muss zwischen 1 und 3 liegen")
            return False

        t = {
            "id": tid,
            "title": title,
            "desc": desc,
            "priority": prio,
            "status": "new",
            "assignee": assignee_id,
            "created": str(datetime.now()),
            "due": due,
        }

        ok = self.db.save_task(tid, t)
        if not ok:
            return False

        u = self.users.get_user(assignee_id)
        if u is not None:
            if prio == 1:
                subject = "Neue Aufgabe (niedrig)"
            elif prio == 2:
                subject = "Neue Aufgabe (mittel)"
            elif prio == 3:
                subject = "Neue Aufgabe (hoch)"
            else:
                subject = "Neue Aufgabe"

            body = "Dir wurde eine neue Aufgabe zugewiesen: " + title

            if mode == 1:
                self.notif.notify(u, "email", subject, body)
            elif mode == 2:
                self.notif.notify(u, "sms", subject, body)
            elif mode == 3:
                self.notif.notify(u, "both", subject, body)
            elif mode == 4:
                self.notif.notify(u, "all", subject, body)

        self.cnt = self.cnt + 1
        log_info("Task " + str(tid) + " erstellt (Anzahl: " + str(self.cnt) + ")")
        return True

    def update_status(self, tid, new_status):
        t = self.db.get_task(tid)
        if t is None:
            log_error("Task nicht gefunden")
            return False

        if new_status not in ["new", "in_progress", "done", "cancelled"]:
            log_error("Unbekannter Status: " + str(new_status))
            return False

        old = t["status"]
        t["status"] = new_status
        self.db.save_task(tid, t)

        if new_status == "done":
            u = self.users.get_user(t["assignee"])
            if u is not None:
                self.notif.notify(
                    u, "email", "Aufgabe erledigt", "Die Aufgabe '" + t["title"] + "' ist erledigt."
                )

        log_info("Task " + str(tid) + ": " + old + " -> " + new_status)
        return True

    def delete_task(self, tid):
        t = self.db.get_task(tid)
        if t is None:
            return False
        self.db.delete_task(tid)
        log_warning("Task " + str(tid) + " geloescht")
        return True

    def get_task(self, tid):
        return self.db.get_task(tid)

    def all_tasks(self):
        return self.db.all_tasks()

    def format_task(self, tid):
        t = self.db.get_task(tid)
        if t is None:
            return "??"
        s = "#" + str(t["id"]) + " " + t["title"]
        if t["priority"] == 1:
            s = s + " [niedrig]"
        elif t["priority"] == 2:
            s = s + " [mittel]"
        elif t["priority"] == 3:
            s = s + " [hoch]"
        s = s + " (" + t["status"] + ")"
        return s

    def find_overdue(self):
        r = []
        for k in self.db.all_tasks():
            t = self.db.all_tasks()[k]
            if t.get("due") is None:
                continue
            if t["status"] == "done" or t["status"] == "cancelled":
                continue
            try:
                due = datetime.fromisoformat(t["due"])
            except:
                continue
            if due < datetime.now():
                r.append(t)
        return r

    def send_reminders(self):
        overdue = self.find_overdue()
        for t in overdue:
            u = self.users.get_user(t["assignee"])
            if u is None:
                continue
            if t["priority"] == 3:
                self.notif.notify_urgent(
                    u, "all", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig!"
                )
            elif t["priority"] == 2:
                self.notif.notify(
                    u, "both", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig."
                )
            else:
                self.notif.notify(
                    u, "email", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig."
                )
```
###  Nachher

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

###  Vorher

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
```python
import json
import os
from abc import ABC, abstractmethod

from logger import log, log_error
from config import DB_FILE, USER_FILE


class Database(ABC):
    """
    Abstrakte Basisklasse für Persistenz.
    Definiert nur die gemeinsame Schnittstelle.
    """

    def __init__(self, file_path):
        self.file_path = file_path
        self.data = {}
        self.load()

    @abstractmethod
    def load(self):
        pass

    @abstractmethod
    def save(self):
        pass

    @abstractmethod
    def delete(self, entity_id):
        pass

    @abstractmethod
    def get(self, entity_id):
        pass

    @abstractmethod
    def get_all(self):
        pass


class TaskRepository(Database):
    """
    Verantwortlich für die Persistenz von Tasks.
    """

    def __init__(self):
        super().__init__(DB_FILE)

    def load(self):
        if os.path.exists(self.file_path):
            try:
                with open(self.file_path, "r") as f:
                    self.data = json.loads(f.read())
            except Exception:
                log_error("Konnte tasks.json nicht laden")
                self.data = {}
        else:
            self.data = {}

    def save(self, task_id=None, task=None):
        if task_id is not None and task is not None:
            if task.get("title") is None or task.get("title") == "":
                log_error("Task ohne Titel kann nicht gespeichert werden")
                return False

            if task.get("priority", 0) < 1 or task.get("priority", 0) > 3:
                log_error("Ungueltige Prioritaet")
                return False

            self.data[str(task_id)] = task

        try:
            with open(self.file_path, "w") as f:
                f.write(json.dumps(self.data))

            log("Tasks gespeichert")
            return True

        except Exception:
            log_error("Tasks konnten nicht gespeichert werden")
            return False

    def get(self, task_id):
        return self.data.get(str(task_id))

    def delete(self, task_id):
        if str(task_id) in self.data:
            del self.data[str(task_id)]
            self.save()

    def get_all(self):
        return self.data


class UserRepository(Database):
    """
    Verantwortlich für die Persistenz von Usern.
    """

    def __init__(self):
        super().__init__(USER_FILE)

    def load(self):
        if os.path.exists(self.file_path):
            try:
                with open(self.file_path, "r") as f:
                    self.data = json.loads(f.read())
            except Exception:
                log_error("Konnte users.json nicht laden")
                self.data = {}
        else:
            self.data = {}

    def save(self, user_id=None, user=None):
        if user_id is not None and user is not None:
            if user.get("name") is None or user.get("name") == "":
                log_error("User ohne Name kann nicht gespeichert werden")
                return False

            self.data[str(user_id)] = user

        try:
            with open(self.file_path, "w") as f:
                f.write(json.dumps(self.data))

            log("Users gespeichert")
            return True

        except Exception:
            log_error("Users konnten nicht gespeichert werden")
            return False

    def get(self, user_id):
        return self.data.get(str(user_id))

    def delete(self, user_id):
        if str(user_id) in self.data:
            del self.data[str(user_id)]
            self.save()

    def get_all(self):
        return self.data
```

### 3.1.3 Open/Close Principle bei `report_generator.py` ist verletzt.
`report_generator.py` besitzt aktuell für jede Berichtsart eine eigene Methode (`daily_report()`, `weekly_report()`, `monthly_report()`). Zusätzlich entscheidet die Methode `email_report()` mittels einer `if`-/`elif`-Verzweigung, welcher Bericht erzeugt werden soll.

Problem an der Jetzigen umsetzung war dass jede neue ReportArt den `report_generator.py` komplett funktional erweitern müsste, somit wird die Datei bei vermehrter Erweiterung unübersichtlich. Es müssen sowohl eine neue Methode implementiert als auch die bestehende Verzweigung in `email_report()` erweitert werden. Dadurch ist die Klasse nicht für Erweiterungen offen.

Um das **Open/Closed Principle** einzuhalten, sollte eine abstrakte Basisklasse `Report` eingeführt werden. Diese definiert lediglich die gemeinsame Schnittstelle zur Berichtserstellung über die Methode `generate()`.

Die konkreten Berichtsarten wie `DailyReport`, `WeeklyReport` und `MonthlyReport` implementieren anschließend jeweils ihre eigene Erzeugungslogik. `ReportGenerator` verwaltet lediglich die verfügbaren Berichtstypen und ruft deren `generate()`-Methode auf. Neue Reporttypen müssen hier nur noch registriert werden, somit sprengen neue Erweiterungen nicht mehr den Dateiumfang.

Theoretisch ließe sich die Berichtserstellung auch generisch lösen, indem Reporttypen parametrisiert verarbeitet werden. Im Rahmen dieses Refactorings wurde darauf bewusst verzichtet, da die Verletzung des Open/Closed Principle bereits durch die Einführung einer gemeinsamen Basisklasse und spezialisierter Berichtsklassen behoben wird. 


### Vorher

```python
from datetime import datetime, timedelta
from database import Database
from user import UserManager
from logger import log


class ReportGenerator:
    def __init__(self):
        self.db = Database()
        self.users = UserManager()

    def daily_report(self):
        o = ""
        o = o + "=== TAGESBERICHT ===\n"
        o = o + "Datum: " + str(datetime.now().date()) + "\n"
        o = o + "\n"

        ts = self.db.all_tasks()
        c = datetime.now() - timedelta(days=1)

        n = 0
        d = 0
        op = 0

        for k in ts:
            t = ts[k]
            try:
                cr = datetime.fromisoformat(t.get("created", ""))
            except:
                continue
            if cr < c:
                continue
            if t["status"] == "new":
                n = n + 1
            elif t["status"] == "done":
                d = d + 1
            else:
                op = op + 1

        o = o + "Neu: " + str(n) + "\n"
        o = o + "Erledigt: " + str(d) + "\n"
        o = o + "Offen: " + str(op) + "\n"
        return o

    def weekly_report(self):
        o = ""
        o = o + "=== WOCHENBERICHT ===\n"
        o = o + "Woche ab: " + str(datetime.now().date()) + "\n"
        o = o + "\n"

        ts = self.db.all_tasks()
        c = datetime.now() - timedelta(days=7)

        n = 0
        d = 0
        op = 0

        for k in ts:
            t = ts[k]
            try:
                cr = datetime.fromisoformat(t.get("created", ""))
            except:
                continue
            if cr < c:
                continue
            if t["status"] == "new":
                n = n + 1
            elif t["status"] == "done":
                d = d + 1
            else:
                op = op + 1

        o = o + "Neu: " + str(n) + "\n"
        o = o + "Erledigt: " + str(d) + "\n"
        o = o + "Offen: " + str(op) + "\n"
        return o

    def monthly_report(self):
        o = ""
        o = o + "=== MONATSBERICHT ===\n"
        o = o + "Monat: " + str(datetime.now().date()) + "\n"
        o = o + "\n"

        ts = self.db.all_tasks()
        c = datetime.now() - timedelta(days=30)

        n = 0
        d = 0
        op = 0

        for k in ts:
            t = ts[k]
            try:
                cr = datetime.fromisoformat(t.get("created", ""))
            except:
                continue
            if cr < c:
                continue
            if t["status"] == "new":
                n = n + 1
            elif t["status"] == "done":
                d = d + 1
            else:
                op = op + 1

        o = o + "Neu: " + str(n) + "\n"
        o = o + "Erledigt: " + str(d) + "\n"
        o = o + "Offen: " + str(op) + "\n"
        return o

    def email_report(self, report_type, recipient):
        if report_type == "daily":
            content = self.daily_report()
        elif report_type == "weekly":
            content = self.weekly_report()
        elif report_type == "monthly":
            content = self.monthly_report()
        else:
            return False

        from email_service import EmailService
        es = EmailService()
        return es.send(recipient, "Bericht: " + report_type, content)
```

### Nachher

#### report.py

```python
from abc import ABC, abstractmethod
from datetime import datetime, timedelta
from task_repository import TaskRepository


class Report(ABC):

    def __init__(self):
        self.repository = TaskRepository()

    @abstractmethod
    def generate(self):
        pass

    def _count_tasks(self, days):
        tasks = self.repository.get_all()

        compare_date = datetime.now() - timedelta(days=days)

        new = 0
        done = 0
        open_tasks = 0

        for task in tasks.values():
            try:
                created = datetime.fromisoformat(task.get("created", ""))
            except ValueError:
                continue

            if created < compare_date:
                continue

            if task["status"] == "new":
                new += 1
            elif task["status"] == "done":
                done += 1
            else:
                open_tasks += 1

        return new, done, open_tasks
```

#### daily_report.py

```python
from datetime import datetime
from report import Report


class DailyReport(Report):

    def generate(self):
        new, done, open_tasks = self._count_tasks(1)

        return (
            "=== TAGESBERICHT ===\n"
            f"Datum: {datetime.now().date()}\n\n"
            f"Neu: {new}\n"
            f"Erledigt: {done}\n"
            f"Offen: {open_tasks}\n"
        )
```

#### weekly_report.py

```python
from datetime import datetime
from report import Report


class WeeklyReport(Report):

    def generate(self):
        new, done, open_tasks = self._count_tasks(7)

        return (
            "=== WOCHENBERICHT ===\n"
            f"Woche ab: {datetime.now().date()}\n\n"
            f"Neu: {new}\n"
            f"Erledigt: {done}\n"
            f"Offen: {open_tasks}\n"
        )
```

#### monthly_report.py

```python
from datetime import datetime
from report import Report


class MonthlyReport(Report):

    def generate(self):
        new, done, open_tasks = self._count_tasks(30)

        return (
            "=== MONATSBERICHT ===\n"
            f"Monat: {datetime.now().date()}\n\n"
            f"Neu: {new}\n"
            f"Erledigt: {done}\n"
            f"Offen: {open_tasks}\n"
        )
```

#### report_generator.py

```python
from email_service import EmailService

from daily_report import DailyReport
from weekly_report import WeeklyReport
from monthly_report import MonthlyReport


class ReportGenerator:

    def __init__(self):
        self.email_service = EmailService()

        self.reports = {
            "daily": DailyReport(),
            "weekly": WeeklyReport(),
            "monthly": MonthlyReport(),
        }

    def generate(self, report_type):
        report = self.reports.get(report_type)

        if report is None:
            return None

        return report.generate()

    def email_report(self, report_type, recipient):
        content = self.generate(report_type)

        if content is None:
            return False

        return self.email_service.send(
            recipient,
            f"Bericht: {report_type}",
            content,
        )
```

### Anpassungen im restlichen Projekt

Alle bisherigen Aufrufe

```python
report_generator.daily_report()
report_generator.weekly_report()
report_generator.monthly_report()
```

werden ersetzt durch

```python
report_generator.generate("daily")
report_generator.generate("weekly")
report_generator.generate("monthly")
```

Die Methode

```python
report_generator.email_report(report_type, recipient)
```

bleibt unverändert bestehen und muss an keiner Stelle angepasst werden.


### 3.1.4 Liskov Substitution Principle bei `user_types.py` verletzt

Die Klasse `User` definiert die Methoden `update_email()` und `delete_account()` so, dass diese erfolgreich ausgeführt werden und einen Rückgabewert liefern. Die Unterklassen `AdminUser` und `ReadOnlyUser` ändern dieses Verhalten jedoch grundlegend.

Während `AdminUser.update_email()` keinen Rückgabewert mehr liefert und `delete_account()` stattdessen eine Ausnahme auslöst, werfen die Methoden der Klasse `ReadOnlyUser` ebenfalls `PermissionError`-Ausnahmen. Dadurch können Objekte dieser Unterklassen nicht mehr überall dort eingesetzt werden, wo ein Objekt der Basisklasse `User` erwartet wird.

Dies verletzt das **Liskov Substitution Principle**, da Unterklassen das Verhalten ihrer Basisklasse nicht verändern dürfen. Jede Unterklasse muss sich so verhalten, dass sie die Basisklasse ohne Seiteneffekte ersetzen kann.

Um das LSP einzuhalten, sollte die Klassenhierarchie so angepasst werden, dass alle Unterklassen dieselben Verträge der Basisklasse erfüllen. 

Statt das Verhalten durch Ausnahmen zu verändern, werden optionale Fähigkeiten über zusätzliche Schnittstellen oder Komposition modelliert.
Beispielsweise kann eine Basisklasse `User` ausschließlich gemeinsame Eigenschaften und Leseoperationen enthalten,
während schreibende oder administrative Funktionen in separate Rollen ausgelagert werden.

Das Resultat ermöglicht ein frei kombinierbares Permissionssystem.

### Vorher
#### user_types.py
```python
class User:
    """Basisklasse fuer alle Benutzer."""

    def __init__(self, uid, name, email):
        self.uid = uid
        self.nm = name
        self.em = email

    def update_email(self, new_email):
        """Aktualisiert die E-Mail. Gibt True zurueck bei Erfolg."""
        self.em = new_email
        return True

    def delete_account(self):
        """Loescht den Account. Gibt True zurueck bei Erfolg."""
        return True

    def get_info(self):
        return self.nm + " (" + self.em + ")"


class AdminUser(User):
    def __init__(self, uid, name, email):
        super().__init__(uid, name, email)
        self.p = ["read", "write", "delete", "admin"]

    def update_email(self, new_email):
        self.em = new_email

    def delete_account(self):
        raise PermissionError("Admin-Accounts koennen nicht geloescht werden")


class ReadOnlyUser(User):
    """Benutzer ohne Schreibrechte."""

    def update_email(self, new_email):
        raise PermissionError("ReadOnly-Benutzer koennen ihre E-Mail nicht aendern")

    def delete_account(self):
        raise PermissionError("ReadOnly-Benutzer koennen nicht geloescht werden")
```


### Nachher

Es ist besser die Types in eigenen Dateien abzubilden. Python ermöglicht zwar im Vergleich zu anderen Programmierprachen wie JAVA, das mehrere
Klassen in einer Datei sind, jedoch geht dadurch die übersicht verloren.

#### user.py

```python
class User:

    def __init__(self, uid, name, email):
        self.uid = uid
        self.name = name
        self.email = email

    def get_info(self):
        return f"{self.name} ({self.email})"
```

#### editable_user.py

```python
from abc import ABC, abstractmethod
from user import User


class EditableUser(User, ABC):

    @abstractmethod
    def update_email(self, new_email):
        pass
```

#### deletable_user.py

```python
from abc import ABC, abstractmethod
from user import User


class DeletableUser(User, ABC):

    @abstractmethod
    def delete_account(self):
        pass
```

#### standard_user.py

```python
from editable_user import EditableUser
from deletable_user import DeletableUser


class StandardUser(EditableUser, DeletableUser):

    def update_email(self, new_email):
        self.email = new_email
        return True

    def delete_account(self):
        return True
```

#### admin_user.py

```python
from editable_user import EditableUser


class AdminUser(EditableUser):

    def __init__(self, uid, name, email):
        super().__init__(uid, name, email)
        self.permissions = ["read", "write", "delete", "admin"]

    def update_email(self, new_email):
        self.email = new_email
        return True
```

#### readonly_user.py

```python
from user import User


class ReadOnlyUser(User):
    pass
```

### Anpassungen im restlichen Projekt

Alle Stellen, an denen bislang vorausgesetzt wurde, dass jeder `User` die Methoden

```python
user.update_email(...)
user.delete_account()
```

besitzt, müssen angepasst werden.

Stattdessen wird geprüft, ob der Benutzer die entsprechende Fähigkeit unterstützt:

```python
if isinstance(user, EditableUser):
    user.update_email("neue@email.de")

if isinstance(user, DeletableUser):
    user.delete_account()
```

Dadurch besitzen nur noch Benutzer, die die jeweilige Fähigkeit tatsächlich unterstützen, die entsprechenden Methoden. 
Gleichzeitig bleiben alle Unterklassen vollständig kompatibel zur Basisklasse `User`, 
wodurch das Liskov Substitution Principle eingehalten wird.

### 3.1.5 Dependency Inversion Principle bei `task_manager.py` verletzt
`task_manager.py` erzeugt die benötigten Abhängigkeiten derzeit selbst.
Im Konstruktor der Klasse `TaskManager` werden beispielsweise `Database`, `NotificationCenter`, `EmailService` und `UserManager` direkt instanziiert.
Dadurch hängt das High-Level-Modul `TaskManager` unmittelbar von konkreten Implementierungen der Low-Level-Module ab. 
Änderungen an diesen Klassen oder deren Konstruktoren erfordern Anpassungen am `TaskManager`. Darüber hinaus wird das Testen erschwert, da sich konkrete Implementierungen nicht ohne Weiteres durch Mock-Objekte oder alternative Implementierungen ersetzen lassen.
Dies verletzt das **Dependency Inversion Principle**, nach dem High-Level-Module nicht von konkreten Implementierungen, sondern von Abstraktionen abhängig sein sollen.

Um das DIP einzuhalten, werden die benötigten Abhängigkeiten über den Konstruktor in den `TaskManager` injiziert (Constructor Injection). Dadurch kennt `TaskManager` lediglich die benötigten Schnittstellen und ist nicht mehr für die Erzeugung der Objekte verantwortlich. Die Instanziierung erfolgt stattdessen außerhalb der Klasse, beispielsweise im Programmstart (`main.py`) oder durch einen Dependency-Container.

### Vorher

```python

from datetime import datetime, timedelta
from database import Database
from email_service import EmailService
from notifications import NotificationCenter
from user import UserManager
from logger import log, log_error, log_info, log_warning


class TaskManager:
    def __init__(self):
        self.db = Database()
        self.email = EmailService()
        self.notif = NotificationCenter()
        self.users = UserManager()
        self.cnt = 0

    def create_task(self, tid, title, desc, prio, assignee_id, due=None, mode=1):
        if title is None or title == "":
            log_error("Titel darf nicht leer sein")
            return False
        if prio < 1 or prio > 3:
            log_error("Prioritaet muss zwischen 1 und 3 liegen")
            return False

        t = {
            "id": tid,
            "title": title,
            "desc": desc,
            "priority": prio,
            "status": "new",
            "assignee": assignee_id,
            "created": str(datetime.now()),
            "due": due,
        }

        ok = self.db.save_task(tid, t)
        if not ok:
            return False

        u = self.users.get_user(assignee_id)
        if u is not None:
            if prio == 1:
                subject = "Neue Aufgabe (niedrig)"
            elif prio == 2:
                subject = "Neue Aufgabe (mittel)"
            elif prio == 3:
                subject = "Neue Aufgabe (hoch)"
            else:
                subject = "Neue Aufgabe"

            body = "Dir wurde eine neue Aufgabe zugewiesen: " + title

            if mode == 1:
                self.notif.notify(u, "email", subject, body)
            elif mode == 2:
                self.notif.notify(u, "sms", subject, body)
            elif mode == 3:
                self.notif.notify(u, "both", subject, body)
            elif mode == 4:
                self.notif.notify(u, "all", subject, body)

        self.cnt = self.cnt + 1
        log_info("Task " + str(tid) + " erstellt (Anzahl: " + str(self.cnt) + ")")
        return True

    def update_status(self, tid, new_status):
        t = self.db.get_task(tid)
        if t is None:
            log_error("Task nicht gefunden")
            return False

        if new_status not in ["new", "in_progress", "done", "cancelled"]:
            log_error("Unbekannter Status: " + str(new_status))
            return False

        old = t["status"]
        t["status"] = new_status
        self.db.save_task(tid, t)

        if new_status == "done":
            u = self.users.get_user(t["assignee"])
            if u is not None:
                self.notif.notify(
                    u, "email", "Aufgabe erledigt", "Die Aufgabe '" + t["title"] + "' ist erledigt."
                )

        log_info("Task " + str(tid) + ": " + old + " -> " + new_status)
        return True

    def delete_task(self, tid):
        t = self.db.get_task(tid)
        if t is None:
            return False
        self.db.delete_task(tid)
        log_warning("Task " + str(tid) + " geloescht")
        return True

    def get_task(self, tid):
        return self.db.get_task(tid)

    def all_tasks(self):
        return self.db.all_tasks()

    def format_task(self, tid):
        t = self.db.get_task(tid)
        if t is None:
            return "??"
        s = "#" + str(t["id"]) + " " + t["title"]
        if t["priority"] == 1:
            s = s + " [niedrig]"
        elif t["priority"] == 2:
            s = s + " [mittel]"
        elif t["priority"] == 3:
            s = s + " [hoch]"
        s = s + " (" + t["status"] + ")"
        return s

    def find_overdue(self):
        r = []
        for k in self.db.all_tasks():
            t = self.db.all_tasks()[k]
            if t.get("due") is None:
                continue
            if t["status"] == "done" or t["status"] == "cancelled":
                continue
            try:
                due = datetime.fromisoformat(t["due"])
            except:
                continue
            if due < datetime.now():
                r.append(t)
        return r

    def send_reminders(self):
        overdue = self.find_overdue()
        for t in overdue:
            u = self.users.get_user(t["assignee"])
            if u is None:
                continue
            if t["priority"] == 3:
                self.notif.notify_urgent(
                    u, "all", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig!"
                )
            elif t["priority"] == 2:
                self.notif.notify(
                    u, "both", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig."
                )
            else:
                self.notif.notify(
                    u, "email", "Aufgabe ueberfaellig", "Die Aufgabe '" + t["title"] + "' ist ueberfaellig."
                )
```

### Nachher

#### task_manager.py

```python
class TaskManager:

    def __init__(
        self,
        database,
        notification_center,
        email_service,
        user_manager,
    ):
        self.database = database
        self.notification_center = notification_center
        self.email_service = email_service
        self.user_manager = user_manager
```

#### main.py

```python
from task_manager import TaskManager
from task_repository import TaskRepository
from notification_center import NotificationCenter
from email_service import EmailService
from user_manager import UserManager


database = TaskRepository()
notification_center = NotificationCenter()
email_service = EmailService()
user_manager = UserManager()

task_manager = TaskManager(
    database=database,
    notification_center=notification_center,
    email_service=email_service,
    user_manager=user_manager,
)
```

### Anpassungen im restlichen Projekt

Alle bisherigen Stellen, an denen `TaskManager` ohne Parameter erzeugt wurde,

```python
task_manager = TaskManager()
```

werden ersetzt durch

```python
task_manager = TaskManager(
    database,
    notification_center,
    email_service,
    user_manager,
)
```

Dadurch ist `TaskManager` nicht länger an konkrete Implementierungen gekoppelt.
Für Tests können beispielsweise Mock-Implementierungen oder Test-Repositories übergeben werden, ohne die Klasse selbst ändern zu müssen.
Ebenso können alternative Persistenz- oder Benachrichtigungssysteme integriert werden, 
ohne den bestehenden Code des `TaskManager` anzupassen oder konkrete Abhängigkeiten innerhalb der Klasse ersetzen zu müssen.
Darüber hinaus lassen sich Konfigurationen, beispielsweise für die verwendete Datenbank, zentral verwalten und abstrahieren. 
Dadurch kann zwischen unterschiedlichen Implementierungen oder Konfigurationen gewechselt werden, 
ohne Änderungen an der Geschäftslogik des `TaskManager` vorzunehmen.

## 3.2 Einsatz von mehreren Mustern (3)


## 3.3 ADRs für Refactoring anhand SOLID-korrektur und Patterns
# ADR-001: Einführung einer hexagonalen Architektur

- **Status:** Akzeptiert

## Kontext

Die bisherige Architektur koppelt die Geschäftslogik direkt an technische Komponenten wie JSON-Speicherung, Benachrichtigungsdienste und die Kommandozeilenoberfläche. Klassen wie der `TaskManager` erzeugen konkrete Abhängigkeiten selbst und übernehmen mehrere Verantwortlichkeiten.

Dadurch sind einzelne Komponenten schwer austauschbar und nur mit hohem Aufwand isoliert testbar.

## Entscheidung

Das Projekt wird nach dem Prinzip der **hexagonalen Architektur (Ports and Adapters)** strukturiert.

Das System wird in folgende Bereiche aufgeteilt:

- Domain
- Application mit Use Cases und Ports
- Inbound Adapter
- Outbound Adapter
- Infrastructure

Die Abhängigkeiten zeigen grundsätzlich von außen nach innen. Die Domain bleibt vollständig unabhängig von technischen Komponenten.

## Begründung

Die hexagonale Architektur trennt Fachlogik und technische Umsetzung. Dadurch kann beispielsweise die JSON-Speicherung später durch SQLite oder eine andere Persistenzlösung ersetzt werden, ohne die Use Cases oder die Domain zu verändern.

Außerdem können unterschiedliche Benutzeroberflächen wie eine CLI oder eine REST-API dieselben Use Cases verwenden.

## Konsequenzen

### Vorteile

- Geringere Kopplung zwischen Fachlogik und Infrastruktur
- Bessere Testbarkeit durch klar definierte Ports
- Austauschbare technische Komponenten
- Klare Trennung von Verantwortlichkeiten
- Bessere Einhaltung der SOLID-Prinzipien

### Nachteile

- Mehr Klassen und Schnittstellen
- Höherer anfänglicher Strukturierungsaufwand
- Für kleine Projekte teilweise mehr Abstraktion als technisch zwingend notwendig

# ADR-002: Verwendung des Command Patterns für Aufgabenaktionen

- **Status:** Akzeptiert

## Kontext

Der bisherige `TaskManager` enthält mehrere unterschiedliche Aktionen:

- Aufgabe erstellen
- Aufgabe löschen
- Status ändern
- Erinnerung versenden
- Aufgaben abrufen

Dadurch wird die Klasse groß und übernimmt mehrere Verantwortlichkeiten.

## Entscheidung

Die Aufgabenaktionen werden mit dem **Command Pattern** in einzelne Klassen aufgeteilt.

Geplante Commands sind:

- `CreateTaskCommand`
- `DeleteTaskCommand`
- `ChangeTaskStatusCommand`
- `SendReminderCommand`

Jedes Command besitzt eine klar abgegrenzte Aufgabe und eine Methode wie `execute()`.

## Begründung

Das Command Pattern passt zu den einzelnen Aktionen des Task-Management-Systems. Jede Aktion kann separat implementiert, getestet und erweitert werden.

Dadurch wird das **Single Responsibility Principle (SRP)** besser eingehalten.

## Konsequenzen

### Vorteile

- Kleinere Klassen
- Bessere Testbarkeit
- Klare Zuordnung von Verantwortlichkeiten
- Einzelne Aktionen können unabhängig erweitert werden

### Nachteile

- Mehr Klassen
- Einfache Aktionen benötigen zusätzliche Struktur
- Die Erzeugung und Übergabe der Commands muss zentral organisiert werden

---

# ADR-003: Verwendung des Strategy Patterns für Benachrichtigungskanäle

- **Status:** Akzeptiert

## Kontext

Das System unterstützt verschiedene Benachrichtigungskanäle:

- E-Mail
- SMS
- Push

Ohne ein eigenes Muster müsste die Auswahl des Kanals durch `if`- oder `elif`-Blöcke erfolgen. Bei jedem neuen Kanal müsste die bestehende Logik geändert werden.

## Entscheidung

Die Benachrichtigungskanäle werden mithilfe des **Strategy Patterns** implementiert.

Alle Benachrichtigungsadapter implementieren den gemeinsamen `NotificationPort`.

Konkrete Strategien sind:

- `EmailNotifier`
- `SmsNotifier`
- `PushNotifier`

Der benötigte Adapter wird von außen an den jeweiligen Use Case übergeben.

Ein separater `NotificationService` wird nicht als Outbound Adapter verwendet.

## Begründung

E-Mail, SMS und Push verfolgen dasselbe Ziel, verwenden jedoch unterschiedliche technische Abläufe.

Das Strategy Pattern ermöglicht es, diese Varianten hinter einer gemeinsamen Schnittstelle zu kapseln. Neue Kanäle können ergänzt werden, ohne bestehende Use Cases zu verändern.

## Konsequenzen

### Vorteile

- Keine langen Fallunterscheidungen für Benachrichtigungskanäle
- Neue Kanäle können als neue Adapter ergänzt werden
- Benachrichtigungsarten sind separat testbar
- Die Auswahl der Strategie erfolgt im Composition Root oder anhand der Anwendungskonfiguration
- Das Open/Closed Principle (OCP) wird besser eingehalten

### Nachteile

- Zusätzliche Klassen und Interfaces
- Die Strategie muss zur Laufzeit ausgewählt und injiziert werden
- Für einfache Anwendungen kann die Architektur komplexer wirken

# ADR-004: Verwendung des Facade Patterns für den Datenzugriff

## Status

**Akzeptiert**

## Kontext

Die Anwendung greift auf verschiedene Komponenten der Datenhaltung zu. Ohne eine gemeinsame Zugriffsschicht müssten Komponenten der Application-Schicht mehrere Repository-Schnittstellen direkt verwenden.

Dadurch entstehen zusätzliche Abhängigkeiten und die interne Struktur der Datenhaltung wird für aufrufende Komponenten sichtbar.

## Entscheidung

Für den Datenzugriff wird das **Facade Pattern** eingesetzt.

Eine `DatabaseFacade` stellt eine zentrale und vereinfachte Schnittstelle für den Zugriff auf die Datenhaltung bereit. Die interne Organisation der Repository-Komponenten wird dabei gekapselt.

## Begründung

Das Facade Pattern reduziert die Komplexität des Datenzugriffs und entkoppelt die Application-Schicht von der internen Struktur der Datenhaltung.

Änderungen an der Organisation oder Implementierung der Repository-Komponenten können vorgenommen werden, ohne dass aufrufende Komponenten angepasst werden müssen.

Die Entscheidung verbessert außerdem die Wartbarkeit und sorgt für eine klarere Trennung der Verantwortlichkeiten.

## Konsequenzen

### Vorteile

- vereinfachter Zugriff auf die Datenhaltung
- geringere Kopplung zwischen Application und Datenzugriff
- zentrale Schnittstelle für Datenoperationen
- bessere Wartbarkeit und Erweiterbarkeit

### Nachteile

- zusätzliche Abstraktionsschicht
- Gefahr einer überladenen Facade bei zu vielen Verantwortlichkeiten
- zusätzlicher Implementierungsaufwand

## Verworfene Alternativen

### Direkter Zugriff auf mehrere Repository-Komponenten

Diese Variante wurde verworfen, da Komponenten der Application-Schicht mehrere Repository-Schnittstellen direkt kennen und verwenden müssten. Dadurch steigt die Kopplung und Änderungen an der Datenhaltung wirken sich stärker auf andere Teile des Systems aus.

### Direkter Zugriff auf konkrete Datenzugriffsklassen

Diese Variante wurde verworfen, da sie die Application-Schicht an konkrete Implementierungen der Datenhaltung koppelt und den Austausch der Speichertechnologie erschwert.


## 3.4 Neue Features (3)









