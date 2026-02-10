# Przewodnik dla Studentów Politechniki Rzeszowskiej

## Wprowadzenie

Witajcie w repozytorium Concordia! Ten dokument został stworzony specjalnie dla studentów
Politechniki Rzeszowskiej pracujących nad projektami związanymi z robotem humanoidalnym
Unitree G1 EDU-U6.

## Czym jest Concordia?

Concordia to potężna biblioteka Python służąca do tworzenia symulacji agentów opartych na
sztucznej inteligencji. Można ją porównać do "wirtualnego świata" gdzie agenci AI mogą:
- Wchodzić w interakcje ze sobą
- Podejmować decyzje
- Reagować na otoczenie
- Prowadzić konwersacje w naturalnym języku

### Dlaczego to jest ważne dla robota Unitree G1?

Robot humanoidalny Unitree G1 EDU-U6 to fizyczna platforma, która może być sterowana
przez inteligentne systemy decyzyjne. Concordia pozwala nam:

1. **Symulować zachowania** - Zanim wgramy kod na robota, możemy przetestować różne
   scenariusze w bezpiecznym środowisku wirtualnym
2. **Trenować agentów** - Możemy stworzyć wirtualnych agentów, którzy nauczą się
   podejmować decyzje w różnych sytuacjach
3. **Testować interakcje społeczne** - Robot będzie wchodził w interakcje z ludźmi,
   więc musimy symulować te interakcje wcześniej
4. **Planować zachowania złożone** - Możemy zaplanować sekwencje działań i sprawdzić
   czy mają one sens przed implementacją na prawdziwym robocie

## Struktura Repozytorium - Krok po Kroku

### 1. Folder `concordia/` - Rdzeń Biblioteki

To tutaj znajduje się cały kod biblioteki. Najważniejsze podfoldery:

#### `concordia/components/`
**Co to jest?** Komponenty to "cegiełki" z których budujemy agentów.

**Dlaczego to ważne?** Każdy agent (a więc i potencjalnie robot) składa się z:
- **Pamięci** - co agent pamięta z przeszłości
- **Obserwacji** - jak agent postrzega otoczenie
- **Planowania** - jak agent decyduje co zrobić
- **Działania** - jak agent wykonuje akcje

**Zastosowanie w robocie G1:**
- Pamięć: Robot pamięta poprzednie interakcje z użytkownikami
- Obserwacja: Robot analizuje dane z czujników i kamery
- Planowanie: Robot decyduje jak zareagować na polecenie
- Działanie: Robot wykonuje ruch lub mówi

#### `concordia/prefabs/`
**Co to jest?** Gotowe "przepisy" na agentów - można je używać od razu lub
modyfikować.

**Dlaczego to ważne?** Zamiast budować agenta od zera, możemy użyć gotowego
szablonu i dostosować go.

**Zastosowanie w robocie G1:**
- Można stworzyć prefab "Robot-Asystent"
- Można stworzyć prefab "Robot-Przewodnik"
- Każdy prefab ma inną osobowość i zachowanie

#### `concordia/environment/`
**Co to jest?** Środowisko symulacji - "świat" w którym działają agenci.

**Dlaczego to ważne?** Definiuje zasady gry - co można robić, jak czas płynie,
jak agenci wchodzą w interakcje.

**Zastosowanie w robocie G1:**
- Symulowane pomieszczenie (np. laboratorium, sala wykładowa)
- Wirtualni ludzie do interakcji
- Przeszkody i obiekty, które robot musi uwzględnić

#### `concordia/language_model/`
**Co to jest?** Połączenie z modelami językowymi (LLM) jak GPT.

**Dlaczego to ważne?** To "mózg" agenta - dzięki niemu agent może:
- Rozumieć język naturalny
- Generować odpowiedzi
- Podejmować inteligentne decyzje

**Zastosowanie w robocie G1:**
- Robot rozumie polecenia głosowe
- Robot może prowadzić konwersację
- Robot może planować działania

### 2. Folder `examples/` - Przykłady do Nauki

To najbardziej przyjazne miejsce do rozpoczęcia nauki!

#### `tutorial.ipynb`
**Cel:** Podstawowy tutorial pokazujący jak skonfigurować symulację.

**Co się nauczysz:**
- Jak zainstalować i skonfigurować Concordia
- Jak stworzyć pierwszego agenta
- Jak uruchomić prostą symulację
- Jak obserwować zachowanie agenta

**Krok po kroku:**
1. Importowanie bibliotek
2. Konfiguracja modelu językowego (LLM)
3. Tworzenie agentów
4. Definiowanie środowiska
5. Uruchamianie symulacji
6. Analiza wyników

#### `dialog.ipynb`
**Cel:** Symulacja dialogu między agentami.

**Zastosowanie w robocie G1:**
- Robot prowadzi konwersację z użytkownikiem
- Robot zadaje pytania wyjaśniające
- Robot reaguje na odpowiedzi użytkownika

#### `marketplace.ipynb`
**Cel:** Bardziej złożona symulacja z wieloma agentami.

**Zastosowanie w robocie G1:**
- Robot w środowisku z wieloma osobami
- Robot negocjuje i podejmuje decyzje
- Robot uczy się z interakcji

## Jak Zacząć - Plan Działania dla Studentów

### Tydzień 1: Podstawy
1. **Przeczytaj** `README_PL.md` - zrozum co to jest Concordia
2. **Zainstaluj** środowisko zgodnie z instrukcjami
3. **Uruchom** `examples/tutorial.ipynb` - krok po kroku
4. **Eksperymentuj** - zmień nazwę agenta, zmień jego opis

### Tydzień 2: Głębsze Zrozumienie
1. **Przestudiuj** `examples/dialog.ipynb` - jak działają konwersacje
2. **Modyfikuj** dialog - dodaj nowego agenta do rozmowy
3. **Czytaj kod** w `concordia/components/` - zrozum jak działają komponenty
4. **Stwórz notatki** - które komponenty będą potrzebne dla robota G1

### Tydzień 3: Integracja z Robotem
1. **Zaplanuj** jakie komponenty potrzebuje robot G1
2. **Stwórz prefab** dla robota - patrz `concordia/prefabs/`
3. **Symuluj scenariusze** - robot witający gości, robot asystujący w laboratorium
4. **Dokumentuj** co działa, a co wymaga poprawy

### Tydzień 4: Zaawansowane Zastosowania
1. **Integracja z czujnikami** - jak dane z robota trafiają do agenta
2. **Integracja z aktuatorami** - jak decyzje agenta sterują robotem
3. **Testowanie** - symuluj różne sytuacje problemowe
4. **Optymalizacja** - usprawniaj działanie systemu

## Kluczowe Koncepcje - Słowniczek

### Agent (Agent)
**Definicja:** Autonomiczna jednostka która postrzega otoczenie i podejmuje działania.

**Analogia:** Myśl o agencie jak o "cyfrowej osobie" z własną osobowością, pamięcią
i celami.

**W robocie G1:** Agent to "umysł" robota - system decyzyjny sterujący jego zachowaniem.

### Game Master (Mistrz Gry)
**Definicja:** Specjalny agent odpowiedzialny za symulację środowiska.

**Analogia:** Jak narrator w grze fabularnej - opisuje świat, rozstrzyga akcje,
wprowadza wydarzenia.

**W robocie G1:** System który interpretuje czujniki robota i przekłada je na
informacje dla agenta, oraz tłumaczy decyzje agenta na komendy dla robota.

### Component (Komponent)
**Definicja:** Modułowy element składowy agenta.

**Analogia:** Jak części samochodu - silnik, koła, kierownica - każdy ma swoją funkcję.

**W robocie G1:**
- Komponent pamięci: Zapamiętuje poprzednie interakcje
- Komponent percepcji: Przetwarza dane z czujników
- Komponent akcji: Generuje komendy ruchu

### Prefab (Prefabrykat)
**Definicja:** Gotowy szablon/przepis na agenta z predefiniowanymi komponentami.

**Analogia:** Jak przepis kulinarny - można go użyć bezpośrednio lub zmodyfikować.

**W robocie G1:** Szablon "Robot-Recepcjonista" z gotowym zestawem zachowań,
można dostosować do konkretnych potrzeb.

### LLM (Large Language Model - Duży Model Językowy)
**Definicja:** Zaawansowany model AI trenowany na dużych zbiorach tekstów, potrafi
rozumieć i generować język naturalny.

**Analogia:** Jak bardzo inteligentny asystent, który rozumie kontekst i może
prowadzić sensowną rozmowę.

**W robocie G1:** "Mózg" który pozwala robotowi rozumieć polecenia głosowe i
generować odpowiedzi w naturalnym języku.

### Memory (Pamięć)
**Definicja:** System przechowywania i odzyskiwania informacji przez agenta.

**Typy pamięci w Concordia:**
- **Pamięć epizodyczna** - konkretne wydarzenia ("rozmawiałem z Janem wczoraj")
- **Pamięć semantyczna** - ogólna wiedza ("roboty nie mogą latać")
- **Pamięć asocjacyjna** - powiązania między pojęciami

**W robocie G1:** Robot pamięta:
- Z kim rozmawiał
- Jakie zadania wykonał
- Czego się nauczył

## Praktyczne Przykłady dla Robota Unitree G1

### Scenariusz 1: Robot-Przewodnik po Politechnice

**Cel:** Robot wita gości i oprowadza ich po uczelni.

**Potrzebne komponenty:**
- Pamięć: Lista sal i ich przeznaczenie
- Obserwacja: Rozpoznawanie osób (czy to gość, czy student)
- Planowanie: Optymalna trasa po budynku
- Działanie: Prowadzenie rozmowy i poruszanie się

**Kod koncepcyjny:**
```python
# To jest uproszczony przykład - szczegóły w tutorial.ipynb

# 1. Tworzymy agenta reprezentującego robota
robot_agent = entity_prefabs.create_agent(
    name="Robot G1",
    description="Przyjazny robot-przewodnik po Politechnice Rzeszowskiej",
    memory_components=[...],  # Pamięć o budynku
    observation_components=[...],  # Percepcja otoczenia
    planning_components=[...],  # Planowanie trasy
    action_components=[...]  # Mówienie i ruch
)

# 2. Definiujemy środowisko (budynek uczelni)
environment = game_master_prefabs.create_environment(
    description="Główny budynek Politechniki Rzeszowskiej",
    locations=["hol główny", "sala 101", "laboratorium", "biblioteka"]
)

# 3. Uruchamiamy symulację
simulation.run(
    agents=[robot_agent, guest_agent],
    environment=environment,
    steps=10  # 10 kroków interakcji
)
```

### Scenariusz 2: Robot-Asystent Laboratoryjny

**Cel:** Robot pomaga studentom w laboratorium, odpowiada na pytania, przynosi narzędzia.

**Potrzebne komponenty:**
- Pamięć: Wiedza o sprzęcie laboratoryjnym
- Obserwacja: Rozpoznawanie obiektów i narzędzi
- Planowanie: Kolejność zadań (priorytetyzacja)
- Działanie: Manipulacja obiektami, udzielanie instrukcji

**Etapy implementacji:**
1. **Symulacja w Concordia** - testowanie logiki decyzyjnej
2. **Implementacja na robocie** - translacja decyzji na ruchy fizyczne
3. **Testowanie** - weryfikacja w realnym laboratorium
4. **Optymalizacja** - poprawa na podstawie doświadczeń

### Scenariusz 3: Robot w Interakcji Społecznej

**Cel:** Robot prowadzi naturalną konwersację, rozpoznaje emocje, reaguje odpowiednio.

**Wyzwania:**
- Rozumienie kontekstu rozmowy
- Rozpoznawanie intencji rozmówcy
- Generowanie odpowiednich odpowiedzi
- Reagowanie na sygnały niewerbalne

**Jak Concordia pomaga:**
- Symulacja tysięcy konwersacji
- Testowanie różnych stylów komunikacji
- Uczenie się z błędów bez ryzyka
- Optymalizacja przed wdrożeniem na robocie

## Często Zadawane Pytania (FAQ)

### Pytanie 1: Czy muszę znać Python aby używać Concordia?
**Odpowiedź:** Tak, podstawowa znajomość Python jest konieczna. Jeśli jeszcze nie znasz
Python, zalecamy rozpoczęcie od podstawowego kursu Python (2-3 tygodnie nauki).

### Pytanie 2: Czy potrzebuję dostępu do płatnych API (jak OpenAI GPT)?
**Odpowiedź:** Concordia może działać z różnymi modelami językowymi. Dla celów
edukacyjnych możecie:
- Użyć darmowych modeli open-source (np. przez TogetherAI)
- Użyć lokalnych modeli (wymaga więcej mocy obliczeniowej)
- Uczestniczyć w programach studenckich oferujących darmowy dostęp do API

### Pytanie 3: Jak długo trwa nauka Concordia?
**Odpowiedź:**
- Podstawy (uruchomienie przykładów): 1-2 dni
- Zrozumienie architektury: 1 tydzień
- Tworzenie własnych symulacji: 2-3 tygodnie
- Zaawansowane zastosowania: 1-2 miesiące praktyki

### Pytanie 4: Czy Concordia działa bezpośrednio z robotem Unitree G1?
**Odpowiedź:** Concordia to biblioteka symulacyjna. Integracja z fizycznym robotem wymaga:
1. Warstwy pośredniej (middleware) łączącej Concordia z API robota
2. Translacji decyzji agenta na komendy robota
3. Translacji danych z czujników robota na obserwacje agenta
Zobacz dokument `INTEGRACJA_ROBOT.md` po szczegóły.

### Pytanie 5: Co jeśli symulacja nie działa jak oczekuję?
**Odpowiedź:**
1. Sprawdź logi - Concordia generuje szczegółowe logi działania
2. Upewnij się, że model językowy jest prawidłowo skonfigurowany
3. Zredukuj złożoność - zacznij od prostszej symulacji
4. Przejrzyj przykłady - porównaj swój kod z działającymi przykładami
5. Poproś o pomoc - użyj issue na GitHub lub zapytaj wykładowcę

## Zasoby Dodatkowe

### Dokumentacja Techniczna
- [README_PL.md](README_PL.md) - Przegląd biblioteki
- [CHEATSHEET_PL.md](CHEATSHEET_PL.md) - Szybki przewodnik po API
- [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) - Integracja z robotem Unitree G1

### Przykłady Kodu
- [examples/tutorial.ipynb](examples/tutorial.ipynb) - Podstawowy tutorial
- [examples/dialog.ipynb](examples/dialog.ipynb) - Symulacja dialogu
- [examples/marketplace.ipynb](examples/marketplace.ipynb) - Złożona symulacja

### Linki Zewnętrzne
- [Tutorial Video (YouTube)](https://youtu.be/2FO5g65mu2I) - Wideo tutorial (po angielsku)
- [Artykuł Naukowy](https://arxiv.org/abs/2312.03664) - Szczegóły techniczne
- [Dokumentacja Unitree G1](https://www.unitree.com/) - Specyfikacja robota

## Plan Projektu - Przykładowy Harmonogram

### Faza 1: Zapoznanie (2 tygodnie)
- [ ] Instalacja środowiska Concordia
- [ ] Przejście przez tutorial.ipynb
- [ ] Uruchomienie przykładów
- [ ] Zrozumienie podstawowych koncepcji

### Faza 2: Projektowanie (2 tygodnie)
- [ ] Określenie scenariusza dla robota G1
- [ ] Zaprojektowanie architektury agenta
- [ ] Wybór odpowiednich komponentów
- [ ] Stworzenie pierwszej symulacji

### Faza 3: Implementacja (4 tygodnie)
- [ ] Implementacja agenta w Concordia
- [ ] Testowanie różnych scenariuszy
- [ ] Optymalizacja zachowania agenta
- [ ] Dokumentacja kodu

### Faza 4: Integracja (4 tygodnie)
- [ ] Nauka API robota Unitree G1
- [ ] Stworzenie warstwy integracyjnej
- [ ] Testy na symulatorze robota
- [ ] Pierwsze testy na prawdziwym robocie

### Faza 5: Finalizacja (2 tygodnie)
- [ ] Testy end-to-end
- [ ] Optymalizacja wydajności
- [ ] Przygotowanie dokumentacji projektu
- [ ] Prezentacja wyników

## Wskazówki dla Powodzenia Projektu

1. **Zacznij od małego** - Nie próbuj stworzyć skomplikowanego systemu od razu.
   Zacznij od prostego agenta, który umie jedno zadanie.

2. **Testuj często** - Po każdej zmianie uruchom symulację i sprawdź czy działa
   jak oczekujesz.

3. **Dokumentuj wszystko** - Zapisuj co działa, co nie działa, dlaczego coś
   zmieniłeś. To zaoszczędzi czas później.

4. **Współpracuj** - Concordia świetnie nadaje się do pracy zespołowej. Podzielcie
   zadania - ktoś robi komponenty, ktoś środowisko, ktoś integrację.

5. **Korzystaj z wersjonowania** - Używajcie Git do śledzenia zmian w kodzie.
   Commitujcie często.

6. **Ucz się od społeczności** - GitHub Concordia ma wiele przykładów i dyskusji.
   Przeglądaj issues i pull requesty.

## Kontakt i Pomoc

Jeśli masz pytania lub problemy:
- Zapytaj wykładowcę podczas laboratorium
- Stwórz issue na GitHub tego repozytorium
- Współpracuj z kolegami z grupy projektowej
- Dokumentuj problemy w wiki projektu

Powodzenia w nauce i projekcie! 🤖🎓
