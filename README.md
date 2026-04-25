# Paris 2024 Olympic Games - Power BI Dashboard

An interactive Power BI report presenting data from the Paris 2024 Olympic Games - medal standings, athlete statistics, event schedule, and the Olympic torch relay route.

---

## Data Source

Data sourced from a public dataset available on [Kaggle](https://www.kaggle.com). The raw data required significant transformation before it could be used in the model.

---

## Data Model

The report is built on 11 tables connected in a mixed star/snowflake schema, covering medallists, medals, results, teams, athletes, coaches, events, venues, schedules, and the torch relay route.

---

## DAX Measures and Calculated Columns

The source medallists data had a wide format with separate boolean columns per medal type. A standard SUM or COUNT would overcount medals at country level because relay events produce multiple rows with the same medal. Dedicated measures use SUMMARIZE to deduplicate by event before aggregating:

```dax
Gold Medals_uniq =
SUMX(
    SUMMARIZE(
        medallists,
        medallists[event],
        "UniqueGold", MAX(medallists[Gold medal])
    ),
    [UniqueGold]
)
```

The same pattern is applied for Silver and Bronze, with Total_uniq summing all three.

Athlete ranking on the Best Athletes page is driven by a calculated column assigning a numeric podium weight to each medal type:

```dax
PodiumRanking =
SWITCH(
    TRUE(),
    medallists[medal_type] = "Gold Medal", 3,
    medallists[medal_type] = "Silver Medal", 2,
    medallists[medal_type] = "Bronze Medal", 1,
    0
)
```

Geographic coordinates for the torch relay route were not present in the source data. Latitude and Longitude calculated columns were added manually using SWITCH() across 70+ cities, including French overseas territories (Cayenne, Noumea, Papeete, Martinique, Guadeloupe):

```dax
Latitude =
SWITCH(
    TRUE(),
    'torch_route'[City] = "Olympia", 37.9733,
    'torch_route'[City] = "Athens", 37.9838,
    'torch_route'[City] = "Paris", 48.8566,
    ...
)
```

---

## Report Structure

### Paris 2024 - Main Page
World map with medal standings by country. Interactive table showing gold, silver, bronze, and total medals. Drill-through to Country Statistic page on row click. Filters: Country, Event, Gender, Discipline, Medal Type.

### Country Statistic
Detailed statistics for a selected country - KPI cards using the deduplicated medal measures, full list of medalists with discipline and event, bar chart showing medal distribution across disciplines. Filters: Discipline, Event, Gender, Medal Type.

### Best Athletes and Teams
Ranking of top athletes by total medals using PodiumRanking weights. Full medalist table sortable by event. Top 3 podium with toggle between Athletes and Teams view. Filters: Event, Country, Medal Type, Gender.

### Schedule
Full schedule of all Olympic sports with venue, start and end dates. Interactive map showing venue locations across France. Filters: Date range, Venue, Sports.

### Torch Route
Animated timeline of the torch relay from the Lighting Ceremony in Olympia (2024-04-16) through Greece and across France to Paris. Coordinates added via calculated columns covering 70+ cities including French overseas territories. Interactive map with city markers and playback controls.

---

## Tools Used

- Power BI Desktop (DAX, Power Query, calculated columns, drill-through, map visuals, play axis)
- Microsoft Azure Maps
- Kaggle dataset

---

## How to Open

Download the `.pbix` file and open it in [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free).

---

## Author

Piotr Szczykutowicz

[LinkedIn](https://www.linkedin.com/in/piotr-szczykutowicz-51b493214/)

---

# Paris 2024 Olympic Games - Dashboard Power BI

Interaktywny raport Power BI prezentujacy dane z Igrzysk Olimpijskich w Paryzu 2024 - klasyfikacja medalowa, statystyki zawodnikow, harmonogram wydarzen i trasa sztafety ognia olimpijskiego.

---

## Zrodlo danych

Dane pochodza z publicznego datasetu dostepnego na [Kaggle](https://www.kaggle.com). Surowe dane wymagaly znacznych transformacji przed uzyciem w modelu.

---

## Model danych

Raport zbudowany jest na 11 tabelach polaczonych w mieszany schemat gwiazdy i platka sniegu, obejmujacych medalistow, medale, wyniki, zespoly, zawodnikow, trenerow, wydarzenia, obiekty, harmonogramy i trase znicza olimpijskiego.

---

## Miary DAX i kolumny obliczane

Dane zrodlowe medallists mialy format szeroki z osobnymi kolumnami logicznymi na typ medalu. Standardowy SUM lub COUNT zawyzylyby medale na poziomie kraju, poniewaz wydarzenia sztafetowe generuja wiele wierszy z tym samym medalem. Dedykowane miary uzywaja SUMMARIZE do deduplikacji po wydarzeniu przed agregacja:

```dax
Gold Medals_uniq =
SUMX(
    SUMMARIZE(
        medallists,
        medallists[event],
        "UniqueGold", MAX(medallists[Gold medal])
    ),
    [UniqueGold]
)
```

Ten sam wzorzec zastosowano dla srebra i brazu, a miara Total_uniq sumuje wszystkie trzy.

Ranking zawodnikow na stronie Best Athletes oparty jest na kolumnie obliczanej przypisujacej wage numeryczna do kazdego typu medalu:

```dax
PodiumRanking =
SWITCH(
    TRUE(),
    medallists[medal_type] = "Gold Medal", 3,
    medallists[medal_type] = "Silver Medal", 2,
    medallists[medal_type] = "Bronze Medal", 1,
    0
)
```

Wspolrzedne geograficzne dla trasy sztafety ognia nie byly obecne w danych zrodlowych. Kolumny obliczane Latitude i Longitude dodano recznie uzywajac SWITCH() dla 70+ miast, w tym francuskich terytoriow zamorskich (Kayenna, Numea, Papeete, Martynika, Gwadelupa):

```dax
Latitude =
SWITCH(
    TRUE(),
    'torch_route'[City] = "Olympia", 37.9733,
    'torch_route'[City] = "Athens", 37.9838,
    'torch_route'[City] = "Paris", 48.8566,
    ...
)
```

---

## Struktura raportu

### Paris 2024 - Main Page
Mapa swiata z klasyfikacja medalowa krajow. Interaktywna tabela pokazujaca zlote, srebrne, brazowe i laczne medale. Drill-through do zakladki Country Statistic po kliknieciu w wiersz. Filtry: Kraj, Wydarzenie, Plec, Dyscyplina, Typ medalu.

### Country Statistic
Szczegolowe statystyki dla wybranego kraju - karty KPI uzywajace zdeduplikowanych miar medalowych, pelna lista medalistow z dyscyplina i wydarzeniem, wykres slupkowy pokazujacy rozklad medali w dyscyplinach. Filtry: Dyscyplina, Wydarzenie, Plec, Typ medalu.

### Best Athletes and Teams
Ranking najlepszych zawodnikow wedlug lacznej liczby medali z wagami PodiumRanking. Pelna tabela medalistow sortowalna wedlug wydarzenia. Podium Top 3 z przelacznikiem miedzy widokiem Zawodnicy i Zespoly. Filtry: Wydarzenie, Kraj, Typ medalu, Plec.

### Schedule
Pelny harmonogram wszystkich dyscyplin olimpijskich z miejscem zawodow, data rozpoczecia i zakonczenia. Interaktywna mapa pokazujaca lokalizacje obiektow we Francji. Filtry: Zakres dat, Obiekt, Dyscyplina.

### Torch Route
Animowana os czasu sztafety ognia od ceremonii rozpalenia w Olimpii (2024-04-16) przez Grecje i Francje az do Paryza. Wspolrzedne dodane jako kolumny obliczane obejmujace 70+ miast w tym francuskie terytoria zamorskie. Interaktywna mapa z oznaczeniami miast i kontrolkami odtwarzania.

---

## Uzyte narzedzia

- Power BI Desktop (DAX, Power Query, kolumny obliczane, drill-through, wizualizacje mapowe, os odtwarzania)
- Microsoft Azure Maps
- Dataset z Kaggle

---

## Jak otworzyc

Pobierz plik `.pbix` i otwórz go w [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (bezplatny).

---

## Autor

Piotr Szczykutowicz

[LinkedIn](https://www.linkedin.com/in/piotr-szczykutowicz-51b493214/)
