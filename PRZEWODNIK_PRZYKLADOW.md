# Przewodnik po Przykładach Concordia

## Wprowadzenie

Ten dokument zawiera szczegółowy opis przykładów dostępnych w folderze `examples/`.
Każdy przykład został wybrany aby pokazać różne możliwości biblioteki Concordia
i pomóc w nauce poprzez praktyczne zastosowania.

## Przegląd Przykładów

| Przykład | Poziom | Czas nauki | Główne Koncepcje |
|----------|--------|-----------|------------------|
| tutorial.ipynb | Początkujący | 1-2 godziny | Podstawowa konfiguracja, prosty agent |
| dialog.ipynb | Początkujący | 1 godzina | Konwersacja między agentami |
| alice.ipynb | Średniozaawansowany | 2-3 godziny | Narracyjna symulacja, wiele agentów |
| selling_cookies.ipynb | Średniozaawansowany | 2-3 godziny | Teoria gier, sceny, decyzje |
| actor_development.ipynb | Zaawansowany | 3-4 godziny | Rozwój agenta w czasie |
| marketplace.ipynb | Zaawansowany | 4-6 godzin | Ekonomia, wymiana, strategie |
| questionnaire_example.ipynb | Średniozaawansowany | 1-2 godziny | Ankiety, eksperyment psychologiczny |

---

## 1. tutorial.ipynb - Twój Pierwszy Kontakt z Concordia

### 📚 Cel Edukacyjny
Nauczyć się podstaw Concordia poprzez stworzenie prostej symulacji od zera.

### 🎯 Co Osiągniesz
- Zrozumiesz podstawową strukturę symulacji
- Stworzysz swojego pierwszego agenta
- Nauczysz się konfigurować model językowy
- Uruchomisz i obserwujesz symulację

### 📖 Struktura Tutorial

#### Sekcja 1: Setup (Konfiguracja)
**Co się dzieje:**
```python
# Instalacja zależności (jeśli używasz Colab)
%pip install --requirement requirements.in
```

**Dlaczego to ważne:**
- Tutorial może być uruchomiony w Google Colab bez lokalnej instalacji
- Colab zapewnia darmowe środowisko do nauki

**Dla robota G1:**
Ta sekcja pokazuje jak przygotować środowisko - podobnie będziesz musiał
zainstalować biblioteki dla integracji z robotem.

#### Sekcja 2: Imports (Importy)
**Co się dzieje:**
```python
from concordia.contrib import language_models
import concordia.prefabs.entity as entity_prefabs
import concordia.prefabs.game_master as game_master_prefabs
```

**Dlaczego to ważne:**
- `language_models` - łączność z LLM (mózg agenta)
- `entity_prefabs` - gotowe szablony agentów
- `game_master_prefabs` - gotowe szablony mistrzów gry

**Dla robota G1:**
To są fundamentalne moduły - w projekcie z robotem będziesz importował
dodatkowo moduły do komunikacji z robotem.

#### Sekcja 3: Language Model Selection (Wybór Modelu Językowego)
**Co się dzieje:**
```python
API_KEY = ''  # Twój klucz API
API_TYPE = 'openai'  # lub 'together_ai'
MODEL_NAME = 'gpt-5'
```

**Dlaczego to ważne:**
- Model językowy to "mózg" który pozwala agentowi myśleć
- Różne modele mają różne możliwości i koszty
- Wybór modelu wpływa na jakość symulacji

**Dla robota G1:**
Robot będzie używał tego samego mechanizmu do podejmowania decyzji.
Dla testów możesz użyć mniejszego, tańszego modelu.

**Porady:**
- Do nauki: `gpt-3.5-turbo` (tani, szybki)
- Do projektów: `gpt-4` (droższy, lepszy)
- Darmowe: modele przez Together AI (np. `llama-2`)

#### Sekcja 4: Creating Agents (Tworzenie Agentów)
**Co się dzieje:**
```python
agent_config = {
    'name': 'Alice',
    'goal': 'Make new friends',
}
```

**Dlaczego to ważne:**
- Nazwa identyfikuje agenta
- Cel (goal) kształtuje zachowanie agenta
- Opcjonalny opis (description) dodaje "osobowość"

**Dla robota G1:**
Robot też będzie agentem z:
- Name: "RobotG1_PRz"
- Goal: "Assist students and staff"
- Description: Szczegółowy opis możliwości i ograniczeń

**Eksperymentuj:**
- Zmień cel agenta i obserwuj jak to wpływa na zachowanie
- Dodaj szczegółowy opis osobowości
- Spróbuj agentów o sprzecznych celach

#### Sekcja 5: Game Master Setup (Konfiguracja Mistrza Gry)
**Co się dzieje:**
```python
game_master = game_master_prefabs.basic_game_master(
    model=model,
    memory=memory,
)
```

**Dlaczego to ważne:**
- Mistrz Gry kontroluje świat symulacji
- Rozstrzyga co się dzieje gdy agent próbuje działać
- Generuje obserwacje dla agentów

**Dla robota G1:**
W projekcie z robotem, Game Master będzie:
- Interpretował dane z czujników → obserwacje dla agenta
- Tłumaczył decyzje agenta → komendy dla robota
- Monitorował bezpieczeństwo

#### Sekcja 6: Running Simulation (Uruchamianie Symulacji)
**Co się dzieje:**
```python
for step in range(10):  # 10 kroków symulacji
    # Agent obserwuje świat
    observation = game_master.get_observation(agent)
    
    # Agent decyduje co zrobić
    action = agent.act(observation)
    
    # Game Master rozstrzyga akcję
    result = game_master.resolve_action(action)
```

**Dlaczego to ważne:**
- To jest rdzeń symulacji - pętla obserwuj→myśl→działaj
- Każdy krok to jedna iteracja tej pętli
- Po zakończeniu możesz przeanalizować co się wydarzyło

**Dla robota G1:**
Dokładnie ta sama pętla będzie działać na robocie:
1. Czujniki → Obserwacja
2. Agent myśli → Decyzja
3. Decyzja → Wykonanie na robocie
4. Powrót do punktu 1

### 🔬 Eksperymenty do Wypróbowania

#### Eksperyment 1: Zmiana Celu Agenta
**Cel:** Zrozumieć jak cel wpływa na zachowanie.

**Co zrobić:**
1. Uruchom tutorial z oryginalnym celem
2. Zmień cel na: "Avoid all social interaction"
3. Porównaj zachowanie

**Czego się nauczysz:**
Cel agenta jest bardzo silnym determinantem zachowania.

#### Eksperyment 2: Dodanie Drugiego Agenta
**Cel:** Zobaczyć interakcję między agentami.

**Co zrobić:**
1. Skopiuj kod tworzący agenta
2. Zmień nazwę i cel
3. Dodaj obu agentów do symulacji

**Czego się nauczysz:**
Jak agenci reagują na siebie nawzajem.

#### Eksperyment 3: Zmiana Długości Symulacji
**Cel:** Obserwować rozwój sytuacji w czasie.

**Co zrobić:**
1. Uruchom z 5 krokami - co się wydarzy?
2. Uruchom z 50 krokami - czy historia się rozwija?

**Czego się nauczysz:**
Długość symulacji wpływa na złożoność interakcji.

### ⚠️ Typowe Problemy i Rozwiązania

#### Problem 1: "API Key Error"
**Przyczyna:** Nie podano klucza API lub klucz jest nieprawidłowy.

**Rozwiązanie:**
```python
# Upewnij się że ustawiłeś klucz:
API_KEY = 'sk-...'  # Twój faktyczny klucz
```

#### Problem 2: "Out of Memory"
**Przyczyna:** Zbyt długa symulacja lub zbyt duży kontekst.

**Rozwiązanie:**
- Zredukuj liczbę kroków symulacji
- Zmniejsz rozmiar pamięci agenta
- Użyj mniejszego modelu

#### Problem 3: Agent Zachowuje się Niespodziewanie
**Przyczyna:** Zbyt ogólny lub sprzeczny opis/cel.

**Rozwiązanie:**
- Bądź bardzo konkretny w opisie agenta
- Jasno określ cel
- Dodaj przykłady pożądanego zachowania

### 🎓 Quiz Sprawdzający

Po przejściu tutorial, odpowiedz na te pytania:

1. **Co to jest prefab?**
   - Odpowiedź: Gotowy szablon/przepis na agenta lub game mastera

2. **Jakie są trzy główne elementy symulacji?**
   - Odpowiedź: Agenci, Game Master, Environment/Premise

3. **Do czego służy model językowy (LLM)?**
   - Odpowiedź: To "mózg" agenta - pozwala mu rozumieć i generować tekst

4. **Co się dzieje w każdym kroku symulacji?**
   - Odpowiedź: Agent obserwuje → myśli → działa

5. **Jak cel (goal) wpływa na agenta?**
   - Odpowiedź: Kształtuje jego decyzje i zachowanie

---

## 2. dialog.ipynb - Symulacja Konwersacji

### 📚 Cel Edukacyjny
Nauczyć się tworzyć naturalne konwersacje między agentami.

### 🎯 Co Osiągniesz
- Stworzysz agentów specjalizujących się w dialogu
- Zrozumiesz jak kontrolować kolejność wypowiedzi
- Nauczysz się analizować konwersacje

### 📖 Kluczowe Koncepty

#### Conversational Entity (Konwersacyjna Encja)
**Co to jest:**
```python
agent = entity_prefabs.conversational__Entity(
    name="Alice",
    # Specjalne komponenty dla konwersacji
)
```

**Dlaczego jest inny niż basic entity:**
- Ma komponenty specyficzne dla dialogu
- Balansuje "zbieżność" (trzymanie się tematu) i "rozbieżność" (wprowadzanie nowych tematów)
- Lepiej radzi sobie z kontekstem konwersacji

**Dla robota G1:**
Robot prowadzący konwersację z użytkownikami powinien używać
`conversational__Entity` zamiast `basic__Entity`.

#### Dialogic Game Master
**Co to jest:**
```python
gm = game_master_prefabs.dialogic__GameMaster(
    acting_order='u-go-i-go',  # Naprzemiennie
)
```

**Opcje acting_order:**
- `'fixed'` - Zawsze ta sama kolejność (A→B→A→B)
- `'random'` - Losowa kolejność każdej tury
- `'u-go-i-go'` - Naprzemiennie (dla 2 agentów)
- `'gm-decides'` - GM wybiera kto mówi (najbardziej naturalne)

**Dla robota G1:**
W rozmowie robot-człowiek, użyj `'u-go-i-go'` lub `'gm-decides'`
aby konwersacja była naturalna.

### 🔬 Eksperymenty

#### Eksperyment 1: Różne Osobowości
Stwórz dwóch agentów o bardzo różnych osobowościach:
- Ekstrawertyk vs Introwertyk
- Optymista vs Pesymista
- Naukowiec vs Artysta

Obserwuj jak różnice wpływają na konwersację.

#### Eksperyment 2: Zmiana Kolejności Wypowiedzi
Przetestuj różne opcje `acting_order` i porównaj:
- Czy `'gm-decides'` tworzy bardziej naturalne konwersacje?
- Czy `'random'` powoduje chaotyczne dialogi?

### 💡 Zastosowania dla Robota G1

#### Scenariusz 1: Robot Recepcjonista
```python
# Robot wita gości i prowadzi uprzejmą konwersację
robot = entity_prefabs.conversational__Entity(
    name="ReceptionBot",
    description="Uprzejmy robot recepcjonista",
)
```

#### Scenariusz 2: Robot-Tutor
```python
# Robot pomaga studentom, zadaje pytania prowadzące
robot = entity_prefabs.conversational__Entity(
    name="TutorBot",
    description="Cierpliwy tutor, używa metody sokratejskiej",
)
```

---

## 3. alice.ipynb - Narracyjna Symulacja

### 📚 Cel Edukacyjny
Stworzyć bogatą, narracyjną symulację z wieloma agentami i złożonymi interakcjami.

### 🎯 Co Osiągniesz
- Zrozumiesz jak budować świat z lokacjami
- Nauczysz się zarządzać wieloma agentami
- Zobaczysz jak emergentne zachowania powstają z prostych reguł

### 📖 Kluczowe Koncepty

#### Locations (Lokacje)
**Co to jest:**
Symulacja może mieć różne miejsca, a agenci mogą się między nimi poruszać.

**Przykład:**
```python
locations = [
    "Rabbit Hole",
    "Hall of Doors",
    "Tea Party",
]
```

**Dla robota G1:**
Robot poruszający się po budynku uczelni będzie śledzić swoją lokację:
- Hol główny
- Sala 101
- Laboratorium
- Biblioteka

#### Situated Game Master
**Co to jest:**
Game Master który zarządza światem z lokacjami.

```python
gm = game_master_prefabs.situated__GameMaster(
    locations=locations,
    agent_locations={'Alice': 'Rabbit Hole'},
)
```

**Dla robota G1:**
GM będzie śledzić gdzie robot się znajduje i co może tam zrobić.

### 🔬 Eksperymenty

#### Eksperyment 1: Rozszerzenie Świata
Dodaj nowe lokacje do świata Alicji:
- Ogród Królowej
- Plażę z Gryfem
- Las Szachowy

Obserwuj jak agenci eksplorują nowe miejsca.

#### Eksperyment 2: Agenci ze Specjalnymi Celami
Daj każdemu agentowi konkretny cel związany z lokacją:
- White Rabbit: "Get to the Queen's castle"
- Alice: "Find the way home"
- Cheshire Cat: "Confuse Alice"

---

## 4. selling_cookies.ipynb - Teoria Gier

### 📚 Cel Edukacyjny
Zrozumieć jak symulować scenariusze z teorii gier z wyraźnymi wyborami i wypłatami.

### 🎯 Co Osiągniesz
- Nauczysz się definiować sceny (Scenes)
- Zrozumiesz action specs (specyfikacje akcji)
- Zobaczysz jak obliczać wypłaty (payoffs)

### 📖 Kluczowe Koncepty

#### Scenes (Sceny)
**Co to jest:**
Scena to strukturyzowana faza symulacji z określonymi regułami.

**Typy scen:**
1. **Conversation Scene** - Swobodna rozmowa
   ```python
   scene = scene_lib.SceneTypeSpec(
       name='conversation',
       action_spec=entity_lib.free_action_spec()
   )
   ```

2. **Decision Scene** - Wybór z opcji
   ```python
   scene = scene_lib.SceneTypeSpec(
       name='decision',
       action_spec=entity_lib.choice_action_spec(
           options=['Buy', 'Don't Buy']
       )
   )
   ```

**Dla robota G1:**
Robot może działać w różnych "trybach":
- Tryb rozmowy (swobodna interakcja)
- Tryb zadania (wykonaj konkretną akcję z listy)
- Tryb nawigacji (wybierz dokąd iść)

#### Payoffs (Wypłaty)
**Co to jest:**
System punktacji za decyzje agentów.

**Przykład:**
```python
def action_to_scores(actions):
    if actions['Alice'] == 'Buy' and actions['Bob'] == 'Sell':
        return {'Alice': -5, 'Bob': +5}  # Alice płaci, Bob zarabia
    return {'Alice': 0, 'Bob': 0}
```

**Dla robota G1:**
Możesz oceniać performance robota:
- +10 punktów za pomyślnie ukończone zadanie
- -5 punktów za kolizję
- +2 punkty za uprzejmą interakcję z człowiekiem

### 🔬 Eksperymenty

#### Eksperyment 1: Dylemat Więźnia
Zaimplementuj klasyczny dylemat więźnia:
- Współpraca / Współpraca: (3, 3)
- Współpraca / Zdrada: (0, 5)
- Zdrada / Współpraca: (5, 0)
- Zdrada / Zdrada: (1, 1)

Obserwuj czy agenci uczą się współpracować.

#### Eksperyment 2: Iterowany Wybór
Uruchom tę samą decyzję wielokrotnie i sprawdź:
- Czy agenci pamiętają przeszłe decyzje?
- Czy rozwijają strategie?
- Czy potrafią negocjować?

### 💡 Zastosowania dla Robota G1

#### Scenariusz: Robot Współpracujący z Ludźmi
Robot musi zdecydować:
- Czy wykonać zadanie sam czy poprosić o pomoc?
- Czy przerwać pracę gdy pojawi się człowiek?

Modeluj to jako grę z wypłatami:
- Sam + Sukces: +5
- Sam + Porażka: -10
- Z pomocą + Sukces: +3 (dzielone)
- Z pomocą + Porażka: -2 (dzielone)

---

## 5. actor_development.ipynb - Rozwój Agenta

### 📚 Cel Edukacyjny
Obserwować jak agent rozwija się i zmienia w czasie poprzez doświadczenia.

### 🎯 Co Osiągniesz
- Zrozumiesz formative memories (wspomnienia formujące)
- Nauczysz się tworzyć agentów z historią
- Zobaczysz długoterminowy rozwój

### 📖 Kluczowe Koncepty

#### Formative Memories Initializer
**Co to jest:**
Specjalny Game Master który tworzy "tło" dla agentów przed główną symulacją.

```python
initializer = game_master_prefabs.formative_memories_initializer__GameMaster(
    num_memories=10,  # Ile wspomnień wygenerować
)
```

**Co generuje:**
- Wspomnienia z dzieciństwa
- Kluczowe wydarzenia życiowe
- Relacje z innymi postaciami

**Dla robota G1:**
Robot może mieć "wspomnienia" z poprzednich interakcji:
- "Ostatnim razem gdy student zapytał o laboratorium X, był zadowolony z odpowiedzi"
- "Gdy robot próbował otworzyć drzwi Y, były zamknięte"

#### Memory Evolution (Ewolucja Pamięci)
**Co się dzieje:**
Pamięć agenta rośnie i zmienia się w czasie:
- Nowe doświadczenia są dodawane
- Stare wspomnienia mogą blaknąć
- Ważne wydarzenia są silniej zapamiętane

### 🔬 Eksperymenty

#### Eksperyment 1: Długoterminowa Symulacja
Uruchom symulację na 100+ kroków i obserwuj:
- Jak zmieniają się relacje między agentami?
- Czy agenci "rosną" jako postacie?
- Czy pamiętają wcześniejsze wydarzenia?

#### Eksperyment 2: Wpływ Przeszłości
Stwórz dwóch identycznych agentów ale daj im różne formative memories:
- Jeden z trudnym dzieciństwem
- Drugi z szczęśliwym dzieciństwem

Czy zachowują się różnie?

### 💡 Zastosowania dla Robota G1

Robot może "uczyć się" z doświadczenia:
- Zapamiętywać preferencje użytkowników
- Unikać błędów które już popełnił
- Rozwijać lepsze strategie interakcji

```python
# Robot pamięta co się sprawdziło
robot.memory.add("Gdy student prosi o cichy pokój, laboratorium A jest najlepsze")
robot.memory.add("Profesor Smith preferuje krótkie odpowiedzi")
```

---

## 6. marketplace.ipynb - Symulacja Ekonomiczna

### 📚 Cel Edukacyjny
Zrozumieć złożone symulacje ekonomiczne z wymianą dóbr i strategiami.

### 🎯 Co Osiągniesz
- Nauczysz się symulować rynek
- Zrozumiesz inwentarz i transakcje
- Zobaczysz emergentne strategie ekonomiczne

### 📖 Kluczowe Koncepty

#### Marketplace Game Master
**Co to jest:**
Specjalistyczny GM do symulacji ekonomicznych.

**Możliwości:**
- Zarządzanie inwentarzem agentów
- Przetwarzanie transakcji kupna/sprzedaży
- Śledzenie cen
- Egzekwowanie reguł rynku

#### Inventory (Inwentarz)
**Co to jest:**
Każdy agent ma inwentarz przedmiotów:
```python
agent.inventory = {
    'apples': 5,
    'coins': 100,
}
```

#### Trading (Handel)
**Jak działa:**
1. Agent A oferuje przedmiot za cenę
2. Agent B może zaakceptować lub odrzucić
3. GM przetwarza transakcję
4. Inwentarze są aktualizowane

### 🔬 Eksperymenty

#### Eksperyment 1: Różne Strategie Cenowe
Stwórz agentów z różnymi strategiami:
- Maksymalizacja zysku (sprzedaj drogo)
- Szybka wymiana (sprzedaj tanio, szybko)
- Akumulacja (kup i trzymaj)

Który agent jest najbardziej sukcessywny?

#### Eksperyment 2: Niedobór i Nadmiar
Manipuluj podażą:
- Co się dzieje gdy jakiś przedmiot jest rzadki?
- Jak reagują ceny?
- Czy agenci dostosowują strategie?

### 💡 Zastosowania dla Robota G1

#### Scenariusz: Robot Zarządzający Zasobami
Robot w laboratorium może zarządzać zasobami:
- Sprzęt (kto go używa, kiedy)
- Materiały eksploatacyjne
- Przestrzeń robocza

```python
robot.inventory = {
    'oscilloscope': {'available': 2, 'reserved': ['Student A']},
    'soldering_iron': {'available': 5, 'reserved': []},
}
```

Robot negocjuje z użytkownikami:
- "Oscyloskop jest zarezerwowany do 15:00, czy może być później?"
- "Jest dostępna lutownica, czy potrzebujesz?"

---

## 7. questionnaire_example.ipynb - Ankiety i Eksperymenty

### 📚 Cel Edukacyjny
Nauczyć się przeprowadzać strukturyzowane eksperymenty i zbierać dane od agentów.

### 🎯 Co Osiągniesz
- Zrozumiesz różnicę między sequential i parallel questionnaires
- Nauczysz się analizować odpowiedzi agentów
- Zobaczysz jak używać Concordia do badań

### 📖 Kluczowe Koncepty

#### Sequential Questionnaire
**Jak działa:**
- Zadawaj pytanie agentowi
- Agent odpowiada
- Agent aktualizuje pamięć (zapamiętuje pytanie i odpowiedź)
- Następne pytanie (może zależeć od poprzedniej odpowiedzi)

**Kiedy użyć:**
- Wywiad (poprzednie odpowiedzi wpływają na kolejne pytania)
- Sekwencja zależnych pytań
- Gdy kontekst jest ważny

#### Parallel Questionnaire
**Jak działa:**
- Wszystkie pytania zadawane naraz (w batch)
- Agent odpowiada na każde niezależnie
- Pamięć NIE jest aktualizowana między pytaniami
- Szybsze (można zrównoleglić)

**Kiedy użyć:**
- Ankiety (pytania niezależne)
- Psychometria
- Zbieranie dużej ilości niezależnych danych

### 🔬 Eksperymenty

#### Eksperyment 1: Porównanie Sequential vs Parallel
Zadaj te same pytania używając obu metod:
- Czy odpowiedzi są różne?
- Która metoda daje bardziej spójne odpowiedzi?

#### Eksperyment 2: Analiza Odpowiedzi
Zbierz odpowiedzi od wielu agentów i analizuj:
- Czy agenci o podobnych celach odpowiadają podobnie?
- Czy można klasyfikować agentów na podstawie odpowiedzi?

### 💡 Zastosowania dla Robota G1

#### Scenariusz 1: Ankieta Satysfakcji
Robot pyta użytkowników o ich doświadczenie:
```python
questions = [
    "Czy robot pomógł Ci dzisiaj?",
    "Jak oceniasz jakość interakcji? (1-10)",
    "Co robot mógłby poprawić?",
]
```

#### Scenariusz 2: Diagnostyka
Robot zadaje pytania aby zdiagnozować problem:
```python
questions = [
    "Gdzie występuje problem?",
    "Kiedy problem się pojawił?",
    "Czy widzisz jakieś komunikaty błędów?",
]
```

Używa sequential questionnaire bo każda odpowiedź wpływa na następne pytanie.

---

## Harmonogram Nauki - Propozycja

### Tydzień 1: Podstawy
- **Dzień 1-2**: `tutorial.ipynb` - Przejdź krok po kroku, eksperymentuj
- **Dzień 3-4**: `dialog.ipynb` - Stwórz własne konwersacje
- **Dzień 5**: Review i notatki - Co zrozumiałeś? Co jest niejasne?

### Tydzień 2: Średniozaawansowane
- **Dzień 1-2**: `alice.ipynb` - Zbuduj własny świat
- **Dzień 3-4**: `selling_cookies.ipynb` - Zaimplementuj grę
- **Dzień 5**: Projekt - Połącz koncepty, stwórz coś własnego

### Tydzień 3: Zaawansowane
- **Dzień 1-2**: `actor_development.ipynb` - Długoterminowa symulacja
- **Dzień 3-4**: `marketplace.ipynb` - Złożone interakcje
- **Dzień 5**: Review - Jak możesz to zastosować do robota G1?

### Tydzień 4: Aplikacja do Robota G1
- **Dzień 1**: Planowanie - Jaki scenariusz dla robota?
- **Dzień 2-3**: Implementacja - Stwórz symulację dla robota
- **Dzień 4**: Testowanie - Czy działa jak oczekiwano?
- **Dzień 5**: Dokumentacja i prezentacja

---

## Częste Pytania o Przykłady

### Pytanie 1: Który przykład powinienem zacząć pierwszy?
**Odpowiedź:** ZAWSZE zacznij od `tutorial.ipynb`. To fundamenty.

### Pytanie 2: Czy muszę przejść wszystkie przykłady?
**Odpowiedź:** Nie musisz, ale zalecane jest przynajmniej:
- tutorial.ipynb (podstawy)
- dialog.ipynb lub alice.ipynb (interakcje)
- Jeden zaawansowany odpowiedni do Twojego projektu

### Pytanie 3: Jak długo zajmuje każdy przykład?
**Odpowiedź:** Zależy od tempa nauki, ale:
- Tylko uruchomienie: 30 min - 1 godz
- Zrozumienie: 1-2 godziny
- Eksperymenty: +2-3 godziny
- Modyfikacje: +3-4 godziny

### Pytanie 4: Co jeśli przykład nie działa?
**Odpowiedź:** 
1. Sprawdź czy masz zainstalowane wszystkie zależności
2. Sprawdź czy klucz API jest poprawny
3. Przeczytaj komunikaty błędów - często wskazują problem
4. Poszukaj w issue na GitHub - może ktoś miał ten sam problem

### Pytanie 5: Czy mogę modyfikować przykłady?
**Odpowiedź:** Tak! To najlepszy sposób nauki. Zalecane modyfikacje:
- Zmiana parametrów
- Dodanie nowych agentów
- Rozszerzenie scenariusza
- Połączenie konceptów z różnych przykładów

---

## Checklist Postępów

Użyj tej checklisty aby śledzić swoje postępy:

### Poziom 1: Podstawy
- [ ] Uruchomiłem tutorial.ipynb
- [ ] Rozumiem co to są prefabs
- [ ] Potrafię stworzyć prostego agenta
- [ ] Rozumiem rolę Game Mastera
- [ ] Uruchomiłem dialog.ipynb
- [ ] Potrafię stworzyć konwersację między agentami

### Poziom 2: Średniozaawansowany
- [ ] Uruchomiłem alice.ipynb lub inne przykłady narracyjne
- [ ] Rozumiem jak działają lokacje
- [ ] Potrafię stworzyć świat z wieloma agentami
- [ ] Uruchomiłem selling_cookies.ipynb
- [ ] Rozumiem sceny i action specs
- [ ] Potrafię zdefiniować grę z wypłatami

### Poziom 3: Zaawansowany
- [ ] Uruchomiłem actor_development.ipynb
- [ ] Rozumiem formative memories
- [ ] Potrafię stworzyć agenta z historią
- [ ] Uruchomiłem marketplace.ipynb
- [ ] Rozumiem symulacje ekonomiczne
- [ ] Potrafię stworzyć złożoną symulację

### Poziom 4: Zastosowanie do Robota G1
- [ ] Zrozumiałem jak Concordia może być użyta z robotem
- [ ] Zaprojektowałem scenariusz dla robota G1
- [ ] Stworzyłem symulację testową
- [ ] Przetestowałem różne strategie
- [ ] Przygotowałem dokumentację

---

## Zasoby Dodatkowe

### Dokumentacja
- [README_PL.md](README_PL.md) - Ogólny przegląd
- [CHEATSHEET_PL.md](CHEATSHEET_PL.md) - Szybka ściągawka
- [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md) - Szczegółowy przewodnik
- [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) - Integracja z Unitree G1

### Społeczność
- GitHub Issues - Zgłaszaj problemy i pytaj
- Discussion Forum - Dyskusje z innymi użytkownikami

### Dalsze Czytanie
- [Artykuł o Concordia](https://arxiv.org/abs/2312.03664)
- [Tutorial Video](https://youtu.be/2FO5g65mu2I)

---

Powodzenia w nauce Concordia! 🚀📚
