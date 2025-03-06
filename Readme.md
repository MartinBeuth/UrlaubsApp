# Urlaubsapp
## allgemein
Diese App wurde komplett mit PowerApps erstellt und dient der Urlaubsverwaltung. Sie bietet Funktionen zur Auswahl von Mitarbeitern, Monatsübersichten und Urlaubsanträgen. 

Die App integriert relationale Daten aus verschiedenen SharePoint-Listen. Logische Verknüpfungen gewährleisten Konsistenz zwischen Kalender- und Mitarbeiterdaten. Der 36-Tage-Zyklus unterteilt sich in sechs gleichmäßige Wochen, was eine übersichtliche Visualisierung ermöglicht.

---
 
 # Inhaltsverzeichnis

| Screen         | Link                  |
|----------------|-----------------------|
| Vorabauswahl   | [Vorabauswahl](#vorabauswahl) |
| Monatsansicht  | [Monatsansicht](#monatsansicht) |
| Antragsseite   | [Antragsseite](#antragsseite) |
| PopUP          | [PopUP](#popup)       |
| PopUP_Storno   | [PopUP_Storno](#popup_storno) |




## Vorabauswahl

  **Code:** 
  
    Sort(
    
    [
    
    "GBM",
    
    "SRO"
    
    ],
    
    Value
    
    )

**Erklärung:** 
Die Power FX-Formel sortiert die Liste ["GBM", "SRO"] alphabetisch aufsteigend. 
<br>
   
   **Code:**

     Distinct(Filter(Mitarbeiter_3, Bereich =  Dropdown2.Selected.Value  && Gewerblich =  true), Gruppe.Value)

<br>

**Erklärung:**
Der Code filtert die Tabelle "Mitarbeiter_3" nach gewerblichen Mitarbeitern im Bereich des in Dropdown2 ausgewählten Werts und gibt eine Liste eindeutiger Gruppen zurück.

---
## Monatsansicht

**Element:** Button Zurück
**Eigenschaft:** OnSelect

**Code:**

    Set(varToday,  DateAdd(Date(Year(varToday),  Month(varToday),  1), -1,  TimeUnit.Months));
    
    //Set(varToday, DateAdd(varToday, -36, TimeUnit.Days));
    
      
      
    
    ClearCollect(
    
    Coll_CurrentMonth,
    
    ForAll(
    
    Sequence(36),  // Dynamische Anzahl der Tage basierend auf dem Monat
    
    {
    
    Day: Text(
    
    DateAdd(
    
    DateValue(Text(Year(varToday),  "[$-de-DE]")  &  "-"  &  Text(Month(varToday),  "[$-de-DE]")  &  "-01"),
    
    ThisRecord.Value  -  1,
    
    TimeUnit.Days
    
    ),
    
    "[$-de-DE]dddd"
    
    ),
    
    Date: DateAdd(
    
    DateValue(Text(Year(varToday),  "[$-de-DE]")  &  "-"  &  Text(Month(varToday),  "[$-de-DE]")  &  "-01"),
    
    ThisRecord.Value  -  1,
    
    TimeUnit.Days
    
    )
    
    }
    
    )
    
    );

**Erklärung:**
Dieser Code erstellt eine Sammlung namens "Coll_CurrentMonth" für einen bestimmten Monat. Er beginnt damit, das Datum auf den letzten Tag des Vormonats zu setzen. Dann generiert er eine Liste von 36 Tagen, beginnend mit dem ersten Tag des Monats. Für jeden Tag werden zwei Informationen gespeichert: der Wochentag (z.B. "Montag") und das vollständige Datum.

**Element:** Monatsanzeige
**Eigenschaft:** Text
**Code**

    Switch(
    
    Text(varToday,  "[$-en-US]mmmm"),
    
    "January",  "Januar",
    
    "February",  "Februar",
    
    "March",  "März",
    
    "April",  "April",
    
    "May",  "Mai",
    
    "June",  "Juni",
    
    "July",  "Juli",
    
    "August",  "August",
    
    "September",  "September",
    
    "October",  "Oktober",
    
    "November",  "November",
    
    "December",  "Dezember",
    
    "Unbekannter Monat"
    
    )&  "-"  &  Year(varToday)

**Erklärung:**
Der Code übersetzt den aktuellen Monatsnamen aus Englisch ins Deutsche und kombiniert ihn mit dem Jahr, um einen formatierten Text wie "Februar-2025" zu erstellen.

**Element:** Wochentag
**Eigenschaft:** Text
**Code:**

    Switch(
    
    ThisItem.Day,
    
    "Monday",  "M",
    
    "Tuesday",  "D",
    
    "Wednesday",  "M",
    
    "Thursday",  "D",
    
    "Friday",  "F",
    
    "Saturday",  "S",
    
    "Sunday",  "S"
    
    )
**Erklärung:**
Dieser Code wandelt den vollen englischen Wochentag (z.B. "Monday") in eine einbuchstabige Abkürzung um (z.B. "M" für Montag und Mittwoch, "D" für Dienstag und Donnerstag, usw.).

**Element:** Wochentag
**Eigenschaft:** Fill
**Code:**

    If(
    
    Weekday(ThisItem.Date)  =  1  ||
    
    Weekday(ThisItem.Date)  =  7  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  24)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  25)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  26)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  31)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  1,  1)  ||
    
    Or(
    
    ThisItem.Date  =  Date(2025,  4,  18),
    
    ThisItem.Date  =  Date(2025,  4,  21),
    
    ThisItem.Date  =  Date(2025,  5,  1),
    
    ThisItem.Date  =  Date(2025,  5,  8),
    
    ThisItem.Date  =  Date(2025,  5,  29),
    
    ThisItem.Date  =  Date(2025,  6,  9),
    
    ThisItem.Date  =  Date(2025,  10,  3)
    
    ),
    
    RGBA(255,  99,  71,  0.3),
    
    RGBA(255,  255,  255,  1)
    
    )
**Erklärung:**
Der Code überprüft, ob ein Datum ein Wochenende, ein Feiertag oder ein besonderer Tag ist, und weist ihm eine rötliche Hintergrundfarbe zu, ansonsten bleibt der Hintergrund weiß.


**Element:** Datum_2
**Eigenschaft:** Text
**Code:**

    Text(DateValue(ThisItem.Date),  "dd")
**Erklärung:** 
Der Code gibt den Tag des Monats aus einem Datum als zweistellige Zahl zurück, z. B. "01" für den 1. Tag.

**Element:** Datum_2
**Eigenschaft:** Fill
**Code:**

    If(
    
    Weekday(ThisItem.Date)  =  1  ||
    
    Weekday(ThisItem.Date)  =  7  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  24)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  25)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  26)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  12,  31)  ||
    
    ThisItem.Date  =  Date(Year(ThisItem.Date),  1,  1)  ||
    
    Or(
    
    ThisItem.Date  =  Date(2025,  4,  18),
    
    ThisItem.Date  =  Date(2025,  4,  21),
    
    ThisItem.Date  =  Date(2025,  5,  1),
    
    ThisItem.Date  =  Date(2025,  5,  8),
    
    ThisItem.Date  =  Date(2025,  5,  29),
    
    ThisItem.Date  =  Date(2025,  6,  9),
    
    ThisItem.Date  =  Date(2025,  10,  3)
    
    ),
    
    RGBA(255,  99,  71,  0.3),
    
    RGBA(255,  255,  255,  1)
    
    )
**Erklärung:**
Der Code prüft, ob ein Datum ein Wochenende, ein Feiertag oder ein besonderer Tag ist, und gibt eine rötliche Farbe zurück, wenn ja, ansonsten Weiß.


**Element:** Button3_2
**Eigenschaft:** Fill
**Code:**
  

    If(
    
    CountRows(
    
    Filter(
    
    ThisItem.MitarbeiterDaten,
    
    Status.Value  =  "Genehmigt"  &&
    
    DateValue(ThisItem.Date)  >=  DateValue('Startdatum ')  &&
    
    DateValue(ThisItem.Date)  <=  DateValue('Enddatum ')
    
    )
    
    )  >  0,
    
    RGBA(56,  96,  178,  1),  // Blau für 'Genehmigt' und im Zeitraum
    
    If(
    
    CountRows(
    
    Filter(
    
    ThisItem.MitarbeiterDaten,
    
    Status.Value  =  "in Genehmigung"
    
    )
    
    )  >  0,
    
    RGBA(255,  255,  0,  1),  // Gelb für 'in Genehmigung'
    
    RGBA(0,  0,  0,  0)  // Transparent für alle anderen Fälle
    
    )
    
    )

**Erklärung:**
Dieser Code prüft den Status von Mitarbeiterdaten für ein bestimmtes Datum und weist entsprechende Farben zu. Wenn genehmigte Daten im angegebenen Zeitraum vorliegen, wird Blau verwendet; bei Daten "in Genehmigung" wird Gelb gewählt, ansonsten bleibt es transparent.

**Element:** Daten für ProbePowerApp
**Eigenschaft:** Items
**Code:**
  

    AddColumns(
    
    SortByColumns(
    
    Filter(
    
    Mitarbeiter_3,
    
    Gruppe.Value  =  Dropdown2_2.Selected.Value  && Gewerblich =  true
    
    ),
    
    "Title",
    
    SortOrder.Ascending
    
    )  As MO,
    
    'Abwesenheitsgrund',
    
    Filter(
    
    Coll_FilteredAbwesenheiten,
    
    Value(ID_Mitarbeiter)  = MO.ID
    
    )
    
    )

**Erklärung:**
Dieser Code filtert die Tabelle "Mitarbeiter_3" nach einer ausgewählten Gruppe und gewerblichen Mitarbeitern, sortiert sie alphabetisch nach dem Feld "Title" und fügt eine zusätzliche Spalte `'Abwesenheitsgrund'` hinzu. Diese Spalte enthält gefilterte Daten aus "Coll_FilteredAbwesenheiten", die mit der ID des jeweiligen Mitarbeiters übereinstimmen.

**Element:** TageImMonat3
**Eigenschaft:** Items
**Code:**

    AddColumns(
    
    Coll_CurrentMonth,
    
    'MitarbeiterDaten',
    
    Filter(
    
    ProbePowerApp,
    
    Value(ID_Mitarbeiter)  =  ThisItem.ID
    
    )
    
    )

**Erklärung:**
Dieser Code fügt der Sammlung `Coll_CurrentMonth` eine neue Spalte namens `'MitarbeiterDaten'` hinzu. In dieser Spalte werden für jeden Eintrag in `Coll_CurrentMonth` die gefilterten Daten aus der Tabelle `ProbePowerApp` gespeichert, bei denen die Mitarbeiter-ID (`ID_Mitarbeiter`) mit der ID des aktuellen Elements (`ThisItem.ID`) übereinstimmt.

**Zusammenhang zwischen Daten für ProbePowerApp und TageImMonat3**
Der erste Code bereitet die Daten vor, indem er Mitarbeiterinformationen zu den Kalenderdaten hinzufügt. Der zweite Code nutzt diese angereicherten Daten, um eine spezifische, gefilterte und sortierte Ansicht mit zusätzlichen Abwesenheitsinformationen zu erstellen.

       +-------------------+
       |                   |
       | Coll_CurrentMonth | <-- Kalenderdaten + Mitarbeiterdaten
       |                   |
       +-------------------+
                ∩
       +-------------------+
       |                   |
       | Gefilterte Daten  | <-- Mitarbeiter gefiltert nach Gruppe, Gewerblich + Abwesenheitsgründe
       |                   |
       +-------------------+

**Formallogischer Zusammenhang der beiden Codes:**

1.  **Code 1 (C1):**  
    C1=A∧BC1=A∧B  
    AA: `Coll_CurrentMonth` enthält Kalenderdaten.  
    BB: Mitarbeiterdaten aus `ProbePowerApp` werden hinzugefügt.
2.  **Code 2 (C2):**  
    C2=C1∧C∧DC2=C1∧C∧D  
    CC: Mitarbeiter werden nach Gruppe und Gewerblich gefiltert.  
    DD: Abwesenheitsgründe werden hinzugefügt.
3.  **Gesamtausdruck:**  
    C2=A∧B∧C∧DC2=A∧B∧C∧D  
    Beide Codes sind logisch verknüpft, da der zweite Code (`C2`) auf den Ergebnissen des ersten Codes (`C1`) aufbaut.

| A   | B   | C   | D   | C1 (A∧B) | C2 (A∧B∧C∧D) |
| --- | --- | --- | --- | -------- | ------------- |
| 1   | 1   | 1   | 1   | 1        | 1             |

---
## Antragsseite

**Element:** Urlaubsanspruch
**Eigenschaft:** Text
**Code:**

    "Urlaubsanspruch: "&  Text(30-Value(LookUp('Mitarbeiter_4', Titel =  DataCardValue2.Value).Urlaubstage_Aktuelles_Jahr),"0")

**Erklärung:**
Der Code berechnet und zeigt den verbleibenden Urlaubsanspruch eines Mitarbeiters an, indem er die genommenen Urlaubstage von einem festen Wert (30) abzieht.


**Element:** Urlaubsverbrauch
**Eigenschaft:** Text
**Code:**

    "Urlaubsverbrauch:"  &  Text(LookUp('Mitarbeiter_3', Titel =  DataCardValue2.Value).Urlaubstage_Aktuelles_Jahr)
**Erklärung:**
Der Code zeigt an, wie viele Urlaubstage ein bestimmter Mitarbeiter bereits verbraucht hat.

**Element:** SubmitButton1
**Eigenschaft:** OnSelect
**Code:**

    // 1️⃣ Rücksetzen der Variablen
    
    Set(GesamtUrlaubstage,  0);
    
    Set(VerbleibenderUrlaub,  0);
    
    Set(VerbrauchterUrlaub,  0);
    
      
    
    // 2️⃣ Sammlung für neue Einträge leeren
    
    Clear(SubmitFormCollection);
    
    ClearCollect(
    
    LocalCollection,
    
    Filter(
    
    'ProbePowerApp',
    
    Name =  DataCardValue2.Value
    
    )
    
    );
    
      
    
    // 3️⃣ Prüfen, ob bereits Urlaub im gewählten Zeitraum existiert
    
    If(
    
    CountRows(
    
    Filter(
    
    'ProbePowerApp',
    
    ID_Mitarbeiter =  DataCardValue4.Value  &&
    
    'Startdatum ' <=  DataCardValue7.SelectedDate  &&
    
    'Enddatum ' >=  DataCardValue6.SelectedDate
    
    )
    
    )  >  0,
    
    Notify("Für diesen Zeitraum wurde bereits Urlaub gebucht.",  NotificationType.Error),
    
      
    
    // 4️⃣ Falls kein Urlaub gebucht wurde: Neuen Urlaub erfassen
    
    ForAll(
    
    Sequence(
    
    DateDiff(DataCardValue6.SelectedDate,  DataCardValue7.SelectedDate,  TimeUnit.Days)  +  1
    
    )  As DateSeq,
    
    With(
    
    {
    
    CurrentDate: DateAdd(DataCardValue6.SelectedDate, DateSeq.Value  -  1,  TimeUnit.Days)
    
    },
    
    If(
    
    Not(
    
    Or(
    
    Weekday(CurrentDate)  =  1,  // Sonntag
    
    Weekday(CurrentDate)  =  7,  // Samstag
    
    CurrentDate in  [
    
    Date(2024,  12,  24),
    
    Date(2024,  12,  25),
    
    Date(2024,  12,  26),
    
    Date(2024,  12,  31),
    
    Date(2025,  1,  1),
    
    Date(2025,  4,  18),
    
    Date(2025,  4,  21),
    
    Date(2025,  5,  1),
    
    Date(2025,  5,  8),
    
    Date(2025,  5,  29),
    
    Date(2025,  6,  9),
    
    Date(2025,  10,  3)
    
    ]
    
    )
    
    ),
    
    Collect(
    
    SubmitFormCollection,
    
    {
    
    Name: DataCardValue2.Value,
    
    Startdatum: CurrentDate,
    
    Enddatum: DataCardValue7.SelectedDate,
    
    ID_Mitarbeiter: DataCardValue4.Value,
    
    Gruppe: DataCardValue1.Value,
    
    Abwesenheitsgrund: DataCardValue3.Selected,
    
    ApprovalInfo: DataCardValue5.Value
    
    }
    
    )
    
    )
    
    )
    
    );
    
      
    
    // 5️⃣ Berechnung der Gesamturlaubstage
    
    Set(GesamtUrlaubstage,  CountRows(SubmitFormCollection));
    
      
    
    // 6️⃣ Aktuelle Urlaubsdaten aus 'Mitarbeiter_3' abrufen
    
    Set(
    
    AktuelleUrlaubstage,
    
    LookUp('Mitarbeiter_3', Titel =  DataCardValue2.Value).Urlaubstage_Aktuelles_Jahr
    
    );
    
    Set(
    
    Urlaubstage_Offen,
    
    LookUp('Mitarbeiter_3', Titel =  DataCardValue2.Value).Urlaubstage_Offen
    
    );
    
      
    
    // 7️⃣ Neue Werte berechnen mit zusätzlicher Bedingung für Urlaubstage_Offen < 30 und < 0
    
    Set(
    
    NeueGesamtUrlaubstage,
    
    AktuelleUrlaubstage  +  GesamtUrlaubstage
    
    );
    
      
    
    Set(
    
    Urlaubstage_Offen,
    
    If(
    
    IsBlank(Urlaubstage_Offen),
    
    30  -  GesamtUrlaubstage,
    
    If(
    
    Urlaubstage_Offen  =  30,
    
    30  -  GesamtUrlaubstage,
    
    If(
    
    Urlaubstage_Offen  >  30,
    
    Urlaubstage_Offen  -  GesamtUrlaubstage,
    
    Max(0,  Urlaubstage_Offen  -  GesamtUrlaubstage)  // Falls <30, setze es sicher auf 0 als Minimum
    
    )
    
    )
    
    )
    
    );
    
      
      
    
    // 8️⃣ Falls Urlaubstage_Offen - GesamtUrlaubstage < 0, das Feld deaktivieren
    
    If(
    
    Urlaubstage_Offen  -  GesamtUrlaubstage  <  0,
    
    Set(FeldBlockiert,  true),  // Variable zum Sperren des Feldes setzen
    
    Set(FeldBlockiert,  false)  // Falls die Bedingung nicht zutrifft, bleibt das Feld aktiv
    
    );
    
      
    
    // 9️⃣ Patch()-Funktion zur Aktualisierung der Daten in 'Mitarbeiter_3'
    
    IfError(
    
    Patch(
    
    'Mitarbeiter_3',
    
    LookUp('Mitarbeiter_3', Titel =  DataCardValue2.Value),
    
    {
    
    Urlaubstage_Aktuelles_Jahr: NeueGesamtUrlaubstage,
    
    Urlaubstage_Offen: Urlaubstage_Offen
    
    }
    
    ),
    
    Notify("Fehler beim Speichern der Urlaubsdaten.",  NotificationType.Error)
    
    );
    
      
    
    // 🔟 Bestätigung setzen und zur Bestätigungsseite navigieren
    
    Set(BestätigungErforderlich,  true);
    
    Navigate(PopUP);
    
    );

**Erklärung:**
Dieser Code führt folgende Hauptaufgaben aus:

1.  Er setzt Variablen zurück und leert Sammlungen für neue Einträge.
2.  Er prüft, ob bereits Urlaub im gewählten Zeitraum existiert.
3.  Falls kein Urlaub gebucht wurde, erfasst er neuen Urlaub für jeden Tag im gewählten Zeitraum, außer an Wochenenden und Feiertagen.
4.  Er berechnet die Gesamtzahl der Urlaubstage und aktualisiert die Urlaubsdaten des Mitarbeiters.
5.  Er prüft, ob der Mitarbeiter genug Urlaubstage übrig hat und sperrt gegebenenfalls das Eingabefeld.
6.  Schließlich aktualisiert er die Daten in der Datenbank und navigiert zu einer Bestätigungsseite.

**Element:** CancelButton1
**Eigenschaft:** OnSelect
**Code:**

    Navigate('Kalender Monatsansicht');

**Erklärung:**

Der Code `Navigate('Kalender Monatsansicht');` bewirkt, dass die App zur Bildschirmseite mit dem Namen `'Kalender Monatsansicht'` wechselt. Dies wird häufig in Power Apps verwendet, um zwischen verschiedenen Ansichten oder Seiten der App zu navigieren.

**Element:** Table1
**Eigenschaft:** Items
**Code:**

    SortByColumns(
    
    Filter(
    
    Mitarbeiter_3,
    
    Gruppe.Value  =  Dropdown2_2.Selected.Value  && Gewerblich =  true
    
    ),
    
    "Title",
    
    SortOrder.Ascending
    
    )
**Erklärung:**

Siehe Screen Kalender Monatsansicht. Der Code sucht in der Tabelle `Mitarbeiter_3` nach Mitarbeitern, die zu einer bestimmten Gruppe gehören (ausgewählt im Dropdown-Menü) und gewerblich sind. Danach werden die gefundenen Mitarbeiter alphabetisch nach ihrem Titel sortiert.

---
## PopUP

**Element:** ButtonCanvas1_1
**Eigenschaft:** OnSelect
**Code:**

    Set(BestätigungErforderlich,  false);
    
    // Speichern
    
    SubmitForm(Form1);
    
    Notify("Erfolgreich gespeichert!",  NotificationType.Success);
    
      
    
    Refresh('ProbePowerApp');
    
    Refresh('Mitarbeiter_3');
    
    //Back();
    
    Navigate('Kalender Monatsansicht_1');

**Erklärung:**
Der Code speichert die eingegebenen Daten, zeigt eine Erfolgsmeldung an und aktualisiert die Datenbanken. Danach wechselt er zur Kalenderansicht, damit der Benutzer die Änderungen sehen kann.

---
## PopUP_Storno


**Element:** ButtonCanvas1_2
**Eigenschaft:** OnSelect
**Code:**

    Set(ZuStornieren,  0);
    
    Set(UrlaubsDaten,  Blank());
    
    Set(AktuelleUrlaubstage,  0);
    
    Set(ID_Mitarbeiter,  Blank()); // Zurücksetzen von ID_Mitarbeiter
    
      
    
    // Überprüfen, ob der Datensatz vorhanden ist
    
    If(
    
    IsBlank(SelectedEntry),
    
    Notify("Der ausgewählte Urlaub konnte nicht gefunden werden.",  NotificationType.Error),
    
    // Abrufen der relevanten Daten aus 'MitarbeiterDaten' in SelectedEntry
    
    Set(
    
    UrlaubsDaten,
    
    LookUp(
    
    SelectedEntry.MitarbeiterDaten,  // Zugriff auf MitarbeiterDaten für den aktuellen Datensatz
    
    DateValue('Startdatum ')  <=  DateValue(SelectedEntry.Date)  &&  // Zeitraum des Urlaubs
    
    DateValue('Enddatum ')  >=  DateValue(SelectedEntry.Date)  // Zeitraum des Urlaubs
    
    )
    
    );
    
    // Falls UrlaubsDaten gefunden wurden, fortfahren
    
    If(
    
    !IsBlank(UrlaubsDaten),
    
    // Abrufen von Startdatum, Enddatum, ID_Mitarbeiter und Name
    
    Set(
    
    Startdatum,
    
    UrlaubsDaten.'Startdatum '
    
    );
    
    Set(
    
    Enddatum,
    
    UrlaubsDaten.'Enddatum '
    
    );
    
      
    
    Set(
    
    ID_Mitarbeiter,
    
    UrlaubsDaten.ID_Mitarbeiter
    
    );
    
    // Abrufen des Namens des Mitarbeiters
    
    Set(
    
    MitarbeiterName,
    
    LookUp(Mitarbeiter_3, ID =  ID_Mitarbeiter).Name
    
    );
    
      
    
    // Berechnung der zu stornierenden Urlaubstage
    
    Set(
    
    ZuStornieren,
    
    DateDiff(Startdatum,  Enddatum)  +  1
    
    );
    
      
    
    // Überprüfen, ob der Datensatz in ProbePowerApp existiert und dann entfernen
    
    Remove(
    
    ProbePowerApp,
    
    LookUp(ProbePowerApp, ID_Mitarbeiter = ID_Mitarbeiter &&
    
    DateValue('Startdatum ')  =  Startdatum  &&
    
    DateValue('Enddatum ')  =  Enddatum)
    
    );
    
      
    
    // Aktuellen Wert von Urlaubstage_Aktuelles_Jahr abrufen
    
    Set(
    
    AktuelleUrlaubstage,
    
    LookUp(Mitarbeiter_3, ID =  ID_Mitarbeiter).Urlaubstage_Aktuelles_Jahr
    
    );
    
      
    
    // Aktualisieren der Urlaubstage in Mitarbeiter_3 mit exakter Subtraktion
    
    UpdateIf(
    
    Mitarbeiter_3,
    
    ID =  ID_Mitarbeiter,
    
    {
    
    Urlaubstage_Aktuelles_Jahr: Max(0,  AktuelleUrlaubstage  -  ZuStornieren)
    
    }
    
    );
    
      
    
    // Überprüfung auf Fehler nach dem Aktualisieren
    
    If(
    
    IsEmpty(Errors(Mitarbeiter_3)),
    
    Notify(
    
    "Stornierung erfolgreich durchgeführt! Urlaubstage für den Zeitraum "  &
    
    Text(Startdatum,  "[$-de-DE]dd.mm.yyyy")  &  " bis "  &  Text(Enddatum,  "[$-de-DE]dd.mm.yyyy")  &
    
    " wurden für "  &  MitarbeiterName  &  " aktualisiert.",
    
    NotificationType.Success
    
    ),
    
    Notify(
    
    "Fehler bei der Stornierung: "  &  First(Errors(Mitarbeiter_3)).Message,
    
    NotificationType.Error
    
    )
    
    )
    
    )
    
    );
    
      
    
    // Zurücksetzen aller Variablen
    
    Set(ZuStornieren,  0);
    
    Set(UrlaubsDaten,  Blank());
    
    Set(AktuelleUrlaubstage,  0);
    
    Set(ID_Mitarbeiter,  Blank()); // Zurücksetzen der ID_Mitarbeiter-Variable
    
      
    
    // PopUp schließen und zurück zur vorherigen Seite
    
    UpdateContext({showPopUp: false});
    
    Back()

**Erklärung:**
Dieser Code führt eine Stornierung von Urlaubstagen durch und setzt dabei verschiedene Variablen zurück. Hier ist die Erklärung in einfachen Sätzen:

1.  **Zurücksetzen der Variablen:**  
    Der Code setzt alle relevanten Variablen wie `ZuStornieren`, `UrlaubsDaten`, `AktuelleUrlaubstage` und `ID_Mitarbeiter` auf ihre Standardwerte, um sicherzustellen, dass keine alten Daten verwendet werden.
2.  **Überprüfung des ausgewählten Eintrags:**  
    Es wird geprüft, ob ein gültiger Urlaubsdatensatz (`SelectedEntry`) ausgewählt wurde. Falls nicht, wird eine Fehlermeldung angezeigt.
3.  **Abrufen der Urlaubsdaten:**  
    Wenn der Eintrag existiert, werden die relevanten Daten wie Startdatum, Enddatum und die ID des Mitarbeiters aus der Datenquelle abgerufen.
4.  **Berechnung der zu stornierenden Urlaubstage:**  
    Die Anzahl der Urlaubstage, die storniert werden sollen, wird berechnet (Differenz zwischen Start- und Enddatum + 1).
5.  **Löschen des Urlaubsdatensatzes:**  
    Der entsprechende Datensatz wird aus der Tabelle `ProbePowerApp` entfernt.
6.  **Aktualisierung der Urlaubstage:**  
    Die verbleibenden Urlaubstage des Mitarbeiters werden in der Tabelle `Mitarbeiter_3` aktualisiert, indem die stornierten Tage von den aktuellen Urlaubstagen abgezogen werden (mindestens 0 als Untergrenze).
7.  **Erfolgsmeldung oder Fehleranzeige:**  
    Nach dem Aktualisieren wird entweder eine Erfolgsmeldung angezeigt oder ein Fehler ausgegeben, falls die Aktualisierung fehlschlägt.
8.  **Zurücksetzen und Navigation:**  
    Am Ende werden alle Variablen erneut zurückgesetzt, das Pop-up-Fenster geschlossen und zur vorherigen Seite zurückgekehrt.

**Zusammengefasst:**  
Der Code storniert einen Urlaubseintrag, aktualisiert die verbleibenden Urlaubstage des Mitarbeiters und zeigt eine Erfolgsmeldung oder einen Fehler an. Danach kehrt er zur vorherigen Ansicht zurück.


