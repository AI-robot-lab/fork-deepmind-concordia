# Concordia - Skrócony Przewodnik po Symulacjach

Zwięzły przewodnik po tworzeniu i uruchamianiu symulacji w Concordia.

---

## Podstawowe Koncepcje

| Koncepcja | Opis |
|---------|-------------|
| **Prefab** | Wielokrotnie używany przepis do budowania encji (agenta lub game mastera) |
| **InstanceConfig** | Konfiguracja określająca który prefab użyć i jego parametry |
| **Config** | Pełna konfiguracja symulacji zawierająca prefaby, instancje, założenia |
| **Simulation** | Główny obiekt orkiestrujący encje i game masterów |
| **Entity** | Agent, który może obserwować świat i podejmować akcje |
| **Game Master** | Kontroluje przebieg symulacji, rozwiązuje akcje, generuje obserwacje |
| **AssociativeMemoryBank** | Przechowuje i odzyskuje wspomnienia używając embeddingów |

---

## Minimalna Konfiguracja Symulacji

```python
from concordia.prefabs import entity as entity_prefabs
from concordia.prefabs import game_master as game_master_prefabs
from concordia.prefabs.simulation import generic as simulation
from concordia.typing import prefab as prefab_lib
from concordia.utils import helper_functions

# 1. Załaduj dostępne prefaby
prefabs = {
    **helper_functions.get_package_classes(entity_prefabs),
    **helper_functions.get_package_classes(game_master_prefabs),
}

# 2. Zdefiniuj instancje agentów
instances = [
    prefab_lib.InstanceConfig(
        prefab="basic__Entity",
        role=prefab_lib.Role.ENTITY,
        params={"name": "Alice", "goal": "Nawiązać nowe znajomości"},
    ),
    prefab_lib.InstanceConfig(
        prefab="basic__Entity",
        role=prefab_lib.Role.ENTITY,
        params={"name": "Bob", "goal": "Znaleźć partnera biznesowego"},
    ),
]

# 3. Dodaj game mastera
instances.append(
    prefab_lib.InstanceConfig(
        prefab="dialogic__GameMaster",
        role=prefab_lib.Role.GAME_MASTER,
        params={
            "name": "conversation rules",
            "next_game_master_name": "conversation rules",
        },
    )
)

# 4. Utwórz konfigurację
config = prefab_lib.Config(
    default_premise="Alice i Bob spotykają się w kawiarni.",
    default_max_steps=20,
    prefabs=prefabs,
    instances=instances,
)

# 5. Zainicjalizuj i uruchom symulację
sim = simulation.Simulation(config=config, model=model, embedder=embedder)
results = sim.play()
```

---

## Silniki Symulacji

Silniki kontrolują przepływ czasu, kolejność wykonywania akcji oraz sposób aktualizacji stanu agentów.

| Silnik | Przepływ Wykonywania | Obsługa Stanu | Najlepszy Do |
| :--- | :--- | :--- | :--- |
| **Sequential** | Turowy. Jeden agent działa, potem GM rozwiązuje. | Agenci obserwują każde zdarzenie natychmiast. | Symulacje narracyjne, rozmowy, zależne akcje. |
| **Simultaneous** | Wsadowy. Wszyscy agenci składają akcje, potem GM rozwiązuje wszystkie. | Agenci nie widzą akcji innych do końca rundy. | Rynki, głosowanie, scenariusze teoriogrowe (np. Dylemat Więźnia). |
| **Sequential Questionnaire** | Iteruje przez pytania/agentów jeden po drugim. | Aktualizuje pamięć agenta po każdej odpowiedzi. | Wywiady, sekwencje gdzie poprzedni kontekst wpływa na następną odpowiedź. |
| **Parallel Questionnaire** | Wysyła pytania do agentów równolegle. | **Bezstanowy**: Agenci odpowiadają na podstawie aktualnego stanu bez aktualizacji pamięci podczas przetwarzania wsadu. | Ankiety, psychometria, efektywne zbieranie niezależnych punktów danych. |

**Użycie:**
```python
from concordia.environment.engines import sequential, simultaneous

# 1. Sequential (Domyślny)
engine = sequential.Sequential()

# 2. Simultaneous (Dla szybkich symulacji z jednoczesnym działaniem)
engine = simultaneous.Simultaneous()

simultaneous_simulation = simulation.Simulation(
    config=config,
    model=model,
    embedder=embedder,
    engine=engine,
)
```

---

## Role Prefabów

```python
from concordia.typing import prefab as prefab_lib

prefab_lib.Role.ENTITY       # Agenci działający w świecie
prefab_lib.Role.GAME_MASTER  # Kontroluje symulację, generuje wydarzenia
prefab_lib.Role.INITIALIZER  # Uruchamia się raz, aby ustawić stan początkowy
```

---

## Wbudowane Prefaby

### Prefaby Encji
| Prefab | Opis |
| :--- | :--- |
| `basic__Entity` | Standardowy agent "Trzech Kluczowych Pytań". Określa akcję pytając: *Jaka to sytuacja? Kim jestem? Co bym zrobił?* |
| `basic_with_plan__Entity` | Rozszerza podstawowego agenta generując **Plan** przed działaniem. |
| `basic_scripted__Entity` | Używa "Trzech Kluczowych Pytań" do wewnętrznej myśli, ale stosuje predefiniowany skrypt dla akcji. |
| `conversational__Entity` | Zoptymalizowany do dialogu. Jawnie balansuje "zbieżność" (trzymanie się tematu) i "rozbieżność" (wprowadzanie nowych pomysłów). |
| `minimal__Entity` | Minimalny agent z tylko Pamięcią, Instrukcjami i Obserwacją. Wysoce konfigurowalny do własnych rozszerzeń. |
| `fake_assistant_with_configurable_system_prompt__Entity` | Wrapper sprawiający, że agent zachowuje się jak standardowy Asystent AI (pomocny, nieszkodliwy) lub według własnego system promptu. |

### Prefaby Game Mastera
| Prefab | Opis |
| :--- | :--- |
| `generic__GameMaster` | Elastyczny, wielofunkcyjny GM. Dobry punkt wyjścia do własnych symulacji. |
| `dialogic__GameMaster` | Wyspecjalizowany w czystych rozmowach. Wspiera stałą, losową lub przez GM wybraną kolejność tur. |
| `dialogic_and_dramaturgic__GameMaster` | Zarządza rozmowami ustrukturyzowanymi w **Sceny** (np. "Prolog", "Epizod 1"). |
| `situated__GameMaster` | Zarządza symulacją z określonymi **Lokacjami** i śledzi, gdzie znajdują się agenci. |
| `situated_in_time_and_place__GameMaster` | Najbardziej złożony model świata. Śledzi **Czas** (Zegar) i **Lokacje**, wspiera cykle dzień/noc i przemieszczanie się. |
| `formative_memories_initializer__GameMaster` | **Initializer**: Uruchamia się raz na początku, aby wygenerować historie i wspomnienia z dzieciństwa dla agentów. |
| `interviewer__GameMaster` | Przeprowadza wielokrotnego wyboru lub stałe kwestionariusze dla agentów. |
| `open_ended_interviewer__GameMaster` | Przeprowadza kwestionariusze z pytaniami otwartymi, używając embeddingów do przetwarzania odpowiedzi. |
| `game_theoretic_and_dramaturgic__GameMaster` | Wyspecjalizowany w Grach Macierzowych (np. Dylemat Więźnia) opakowanych w narracyjną Scenę. |
| `marketplace__GameMaster` | Wyspecjalizowany w symulacjach ekonomicznych. Wspiera kupno, sprzedaż i zarządzanie inwentarzem. |
| `psychology_experiment__GameMaster` | Generyczna powłoka do przeprowadzania eksperymentów psychologicznych zdefiniowanych przez niestandardowe komponenty obserwacji/akcji. |
| `scripted__GameMaster` | Wymusza na symulacji podążanie ściśle liniowego skryptu zdarzeń. |

---

## Przykłady Symulacji

Podczas gdy "Minimalna Konfiguracja Symulacji" pokazuje strukturę kodu, oto dwa
popularne *typy* symulacji, które możesz zbudować, ze specyficznymi prefabami i
wymaganą logiką.

### 1. Symulacja Narracyjna (np. Alicja w Krainie Czarów)
**Najlepsza do:** Opowiadania historii, odgrywania ról i otwartych interakcji, gdzie
wynik jest nieznany.

**Kluczowe Komponenty:**

*   **Encje:** `basic__Entity` (standardowi agenci) z opcjonalnymi `goals`.
*   **Game Master:** `generic__GameMaster` (zarządza kolejnością tur i obserwacjami).
*   **Założenie (Premise):** Ustala początkową scenę (np. "Alicja widzi Białego Królika...").

W tym scenariuszu agenci działają na podstawie opisów ich postaci i
`default_premise`. Nie ma ścisłych "zasad gry" ani punktów, tylko interakcja.

```python
# 1. Zdefiniuj Encje
alice = prefab_lib.InstanceConfig(
    prefab='basic__Entity',
    role=prefab_lib.Role.ENTITY,
    params={'name': 'Alice'},
)
rabbit = prefab_lib.InstanceConfig(
    prefab='basic__Entity',
    role=prefab_lib.Role.ENTITY,
    params={
        'name': 'White Rabbit',
        'goal': 'Dotrzeć na czas do królowej',
    },
)

# 2. Generic Game Master (Stała Kolejność)
gm = prefab_lib.InstanceConfig(
    prefab='generic__GameMaster',
    role=prefab_lib.Role.GAME_MASTER,
    params={
        'name': 'default rules',
        'acting_order': 'fixed', # opcje: 'fixed', 'random', lub 'u-go-i-go' (głównie 2 graczy)
    },
)

# 3. Użyj generycznej Symulacji
config = prefab_lib.Config(
    default_premise="Alicja widzi Białego Królika biegnącego z zegarkiem kieszonkowym.",
    default_max_steps=10,
    prefabs=prefabs,
    instances=[alice, rabbit, gm],
)
```

### 2. Symulacja Teoriogrowa (np. Sprzedaż Ciastek)
**Najlepsza do:** Gier (Dylemat Więźnia) i scenariuszy ze specyficznymi "ruchami"
i "wypłatami".

**Kluczowe Komponenty:**

*   **Encje:** `basic__Entity` lub własne agenty.
*   **Game Master:** `game_theoretic_and_dramaturgic__GameMaster`. Ten GM jest kluczowy, ponieważ może:
    *   Egzekwować ustrukturyzowane **Sceny** (np. specyficzne rundy dla dyskusji vs. decyzji).
    *   Obliczać **Wypłaty** używając `action_to_scores`.
    *   Dostarczać informacje zwrotne poprzez `scores_to_observation`.

W tym scenariuszu często mamy fazę "Rozmowy" (wolny tekst), po której następuje
faza "Decyzji" (ograniczony wybór).

```python
from concordia.typing import scene as scene_lib

# 1. Zdefiniuj Sceny
# Scena Rozmowy (Wolna wypowiedź)
conversation_scene = scene_lib.SceneTypeSpec(
    name='conversation',
    game_master_name='conversation rules',
    action_spec=entity_lib.free_action_spec(
        call_to_action=entity_lib.DEFAULT_CALL_TO_SPEECH
    ),
)

# Scena Decyzji (Wybór Binarny)
decision_scene = scene_lib.SceneTypeSpec(
    name='decision',
    game_master_name='decision rules',
    action_spec={
        'Alice': entity_lib.choice_action_spec(
            call_to_action='Kupić ciastka?',
            options=['Tak', 'Nie'],
        ),
    },
)

# Sekwencja Scen
scenes = [
    scene_lib.SceneSpec(
        scene_type=conversation_scene,
        participants=['Alice', 'Bob'],
        num_rounds=4,
        premise={'Alice': ['Bob do ciebie podchodzi.'],
                 'Bob': ['Podchodzisz do Alicji.']},
    ),
    scene_lib.SceneSpec(
        scene_type=decision_scene,
        participants=['Alice'],
        num_rounds=1,
        premise={'Alice': ['Zdecyduj, czy kupić ciastka.']},
    ),
]

# 2. Zdefiniuj Wypłaty (Teoria Gier)
def action_to_scores(joint_action):
    # Jeśli Alice kupi (Tak), traci pieniądze (-1), Bob zyskuje (1)
    if joint_action['Alice'] == 'Tak':
        return {'Alice': -1, 'Bob': 1}
    return {'Alice': 0, 'Bob': 0}

def scores_to_observation(scores):
    return {p: f"Wynikowy Wynik: {s}" for p, s in scores.items()}

# 3. Skonfiguruj Game Masterów
instances = [
    # ... Encje Alice i Bob ...
    prefab_lib.InstanceConfig(
        prefab='game_theoretic_and_dramaturgic__GameMaster',
        role=prefab_lib.Role.GAME_MASTER,
        params={
            'name': 'decision rules',
            'scenes': scenes,
            'action_to_scores': action_to_scores,
            'scores_to_observation': scores_to_observation,
        },
    ),
    prefab_lib.InstanceConfig(
        prefab='dialogic_and_dramaturgic__GameMaster',
        role=prefab_lib.Role.GAME_MASTER,
        params={
            'name': 'conversation rules',
            'scenes': scenes,
        },
    ),
    # Opcjonalnie: Initializer do ustawienia początkowych wspomnień/kontekstu
    prefab_lib.InstanceConfig(
        prefab='formative_memories_initializer__GameMaster',
        role=prefab_lib.Role.INITIALIZER,
        params={
            'name': 'initial setup rules',
            'next_game_master_name': 'conversation rules',
            'shared_memories': ["Alice i Bob są sąsiadami."],
            'player_specific_context': {
                'Alice': "Nie lubisz ciastek.",
                'Bob': "Jesteś sprzedawcą ciastek."
            },
        },
    ),
]
```

---

## Tworzenie Własnego Prefabu

```python
import dataclasses
from concordia.typing import prefab as prefab_lib
from concordia.agents import entity_agent_with_logging
from concordia.components import agent as agent_components

@dataclasses.dataclass
class MyCustomAgent(prefab_lib.Prefab):
    description: str = "Własny agent dla mojej symulacji"

    def build(self, model, memory_bank):
        name = self.params.get("name", "Agent")
        goal = self.params.get("goal", "")

        # Utwórz komponenty
        memory = agent_components.memory.AssociativeMemory(
            memory_bank=memory_bank)
        instructions = agent_components.instructions.Instructions(
            agent_name=name)
        observation = agent_components.observation.LastNObservations(
            history_length=50)

        components = {
            agent_components.memory.DEFAULT_MEMORY_COMPONENT_KEY: memory,
            "Instructions": instructions,
            agent_components.observation.DEFAULT_OBSERVATION_COMPONENT_KEY: observation,
        }

        act_component = agent_components.concat_act_component.ConcatActComponent(
            model=model,
            component_order=list(components.keys()),
        )

        return entity_agent_with_logging.EntityAgentWithLogging(
            agent_name=name,
            act_component=act_component,
            context_components=components,
        )

# Zarejestruj własny prefab
prefabs["my_custom__Entity"] = MyCustomAgent()
```

---

## Zaawansowana Architektura Agenta

Dla bardziej złożonych zachowań możesz łączyć komponenty w łańcuch, gdzie wyjście
jednego komponentu staje się wejściem/kontekstem dla innego. Pozwala to agentom
"myśleć" przed działaniem.

### Kluczowe Komponenty do Rozumowania

*   **`SituationRepresentation`**: Podsumowuje obecną sytuację na podstawie
ostatnich obserwacji + istotnych wspomnień.
*   **`QuestionOfRecentMemories`**: Zadaje konkretne pytanie modelowi na podstawie
pamięci. Użyteczne dla "Zasad Przewodnich" lub "Monologu Wewnętrznego".

### Przykład: Agent "Refleksyjny"
Ten agent najpierw buduje reprezentację sytuacji, potem pyta siebie jak ją wykorzystać,
a *następnie* decyduje o akcji.

```python
from concordia.contrib.components.agent import situation_representation_via_narrative
from concordia.components import agent as agent_components

@dataclasses.dataclass
class ReflectiveAgent(prefab_lib.Prefab):
    def build(self, model, memory_bank):
        name = self.params.get("name", "Agent")
        
        # 1. Podstawowe Komponenty
        instructions = agent_components.instructions.Instructions(
            agent_name=name)
        observation = agent_components.observation.LastNObservations(
            history_length=100)
        memory = agent_components.memory.AssociativeMemory(
            memory_bank=memory_bank)
        
        # 2. Zaawansowane Komponenty (Łańcuch Myślowy)
        
        # Krok A: Podsumuj sytuację
        situation = situation_representation_via_narrative.SituationRepresentation(
            model=model,
            observation_component_key=agent_components.observation.DEFAULT_OBSERVATION_COMPONENT_KEY,
            declare_entity_as_protagonist=True,
        )
        
        # Krok B: Zastosuj Zasadę Przewodnią (używa kontekstu z Kroku A)
        principle = agent_components.question_of_recent_memories.QuestionOfRecentMemories(
            model=model,
            pre_act_label=f"Monolog Wewnętrzny {name}",
            question=f"Jak {name} może najlepiej osiągnąć swoje cele w tej sytuacji?",
            answer_prefix=f"{name} myśli: ",
            add_to_memory=False, # Nie zaśmiecaj pamięci każdą myślą
            components=[
                "Instructions",
                "situation_representation" # <--- Zależy od Kroku A
            ],
        )

        # 3. Złóż Komponenty (Kolejność ma znaczenie!)
        components = {
            "Instructions": instructions,
            agent_components.memory.DEFAULT_MEMORY_COMPONENT_KEY: memory,
            agent_components.observation.DEFAULT_OBSERVATION_COMPONENT_KEY: observation,
            "situation_representation": situation,
            "guiding_principle": principle,
        }
        
        # Komponent Act widzi wszystko w 'components'
        act_component = agent_components.concat_act_component.ConcatActComponent(
            model=model,
            component_order=list(components.keys()),
        )
        
        return entity_agent_with_logging.EntityAgentWithLogging(
            agent_name=name,
            act_component=act_component,
            context_components=components,
        )
```

---

## Inicjalizacja Wspomnień Agenta

Zalecanym sposobem generowania historii agentów i wspólnego kontekstu jest użycie
`formative_memories_initializer__GameMaster`. Ten prefab uruchamia się raz na
początku symulacji, aby wstrzyknąć wspomnienia do agentów.

```python
# Zdefiniuj wspólne fakty (budowanie świata)
shared_memories = [
    "Riverbend to idylliczne miasteczko wiejskie.",
    "Jest rok 2024.",
]

# Zdefiniuj kontekst specyficzny dla gracza (historie)
player_specific_context = {
    "Alice": "Alice jest piekarką, która uwielbia eksperymenty. Jest optymistką.",
    "Bob": "Bob jest sceptycznym dziennikarzem śledczym badającym lokalną korupcję.",
}

# Dodaj Initializer do swojej listy instancji
initializer = prefab_lib.InstanceConfig(
    prefab='formative_memories_initializer__GameMaster',
    role=prefab_lib.Role.INITIALIZER,
    params={
        'name': 'initial setup rules',
        # kluczowe: gdzie przekazać kontrolę po inicjalizacji
        'next_game_master_name': 'conversation rules',
        'shared_memories': shared_memories,
        'player_specific_context': player_specific_context,
    },
)

# Zakładając, że 'instances' jest zdefiniowane gdzie indziej i chcesz to do niego dodać.
# W tym przykładzie pokażemy tylko konfigurację.
# instances.append(initializer)
```

### Wstępne Ładowanie Wspomnień
```python
from concordia.associative_memory import basic_associative_memory

# Utwórz bank pamięci
memory_bank = basic_associative_memory.AssociativeMemoryBank(
    sentence_embedder=embedder
)

# Dodaj wspomnienia
memory_bank.add("Alice uwielbia wędrówki po górach.")
memory_bank.add("Alice pracuje jako inżynier oprogramowania.")

# Przekaż do konfiguracji instancji
instance = prefab_lib.InstanceConfig(
    prefab="basic__Entity",
    role=prefab_lib.Role.ENTITY,
    params={
        "name": "Alice",
        "memory_state": {"buffer": [], "memory_bank": memory_bank.get_state()},
    },
)
```

### Transfer Pamięci Między Fazami
```python
import copy

# Po fazie 1, zapisz stan pamięci
source_entity = phase1_sim.entities[0]
temp_memory = copy.deepcopy(
    source_entity.get_component("__memory__").get_state()
)

# W fazie 2, zastosuj do odpowiedniej encji
target_entity = phase2_sim.entities[0]
target_entity.get_component("__memory__").set_state(temp_memory)
```

---

## Wywoływanie Akcji Agenta

```python
from concordia.typing import entity as entity_lib

# Prompt akcji w wolnej formie
action_spec = entity_lib.free_action_spec(
    call_to_action="Co Alice robi dalej?"
)
response = agent.act(action_spec=action_spec)

# Agent obserwuje coś
agent.observe("Bob macha na powitanie.")
```

---

## Symulacje Równoległe (Przykład 2 dialogów równolegle)

```python
from concordia.utils import concurrency
import functools

def run_dyad_task(player_states, model, embedder):
    sim = create_dialog_simulation(player_states, model, embedder)
    return sim.play()

# Utwórz zadania równoległe
tasks = {
    "alice_bob": functools.partial(run_dyad_task,
        player_states={"Alice": alice_state, "Bob": bob_state},
        model=model, embedder=embedder),
    "carol_dave": functools.partial(run_dyad_task,
        player_states={"Carol": carol_state, "Dave": dave_state},
        model=model, embedder=embedder),
}

# Uruchom wszystkie dyady równolegle
results = concurrency.run_tasks(tasks)
```

---

## Wzorzec Workflow Wielofazowego

```python
# Faza 1: Rynek
marketplace_sim = simulation.Simulation(
    config=marketplace_config, model=model, embedder=embedder
)
marketplace_sim.play()

# Transfer stanu do Fazy 2
entities_for_phase2 = []
for entity in marketplace_sim.entities:
    entities_for_phase2.append(entity)

# Faza 2: Dialog (używając przeniesionych wspomnień)
daily_dyads = generate_dyads(entities_for_phase2)

for p1, p2 in daily_dyads:
    dyad_sim = create_dialog_simulation(p1, p2, model, embedder)
    # Transfer wspomnień
    for src in [p1, p2]:
        tgt = next(e for e in dyad_sim.entities if e.name == src.name)
        mem = copy.deepcopy(src.get_component("__memory__").get_state())
        tgt.get_component("__memory__").set_state(mem)

    dyad_sim.play() # zobacz przykład powyżej dla uruchamiania tej pętli równolegle
```

---

## Referencja Kluczowych Importów

```python
# Podstawowa symulacja
from concordia.prefabs.simulation import generic as simulation
from concordia.typing import prefab as prefab_lib
from concordia.typing import entity as entity_lib

# Biblioteki prefabów
from concordia.prefabs import entity as entity_prefabs
from concordia.prefabs import game_master as game_master_prefabs
from concordia.utils import helper_functions

# Komponenty agenta
from concordia.components import agent as agent_components
from concordia.agents import entity_agent_with_logging

# Pamięć
from concordia.associative_memory import basic_associative_memory

# Współbieżność
from concordia.utils import concurrency

# Silniki
from concordia.environment.engines import sequential, simultaneous

# Zarządzanie scenami
from concordia.typing import scene as scene_lib
```

---

## Tworzenie Własnego Komponentu Game Mastera

Komponenty Game Mastera kontrolują przepływ symulacji. Kluczową metodą jest `pre_act`,
która jest wywoływana z różnymi wartościami `action_spec.output_type` do obsługi
różnych faz. Komponent Game Mastera może kontrolować jeden lub wiele typów
action specs dla Game Mastera, co jest określone w prefabie Game Mastera.

```python
components_of_game_master = {
        _get_class_name(instructions): instructions,
        actor_components.memory.DEFAULT_MEMORY_COMPONENT_KEY: (
            actor_components.memory.AssociativeMemory(memory_bank=memory_bank)
        ),
        # Użyj własnego komponentu Game Mastera do tworzenia obserwacji
        gm_components.make_observation.DEFAULT_MAKE_OBSERVATION_COMPONENT_KEY: (
            my_gamemaster_component
        ),
        next_actor_key: next_actor,
        # Użyj własnego komponentu Game Mastera do następnej specyfikacji akcji
        gm_components.next_acting.DEFAULT_NEXT_ACTION_SPEC_COMPONENT_KEY: (
            my_gamemaster_component
        ),
        # Użyj własnego komponentu Game Mastera do rozwiązywania akcji
        gm_components.switch_act.DEFAULT_RESOLUTION_COMPONENT_KEY: (
            my_gamemaster_component
        ),
    }
```

### Podstawowa Struktura Komponentu GM

```python
import dataclasses
from concordia.typing import entity as entity_lib
from concordia.typing import entity_component

class MyGameMasterComponent(
    entity_component.ContextComponent,
    entity_component.ComponentWithLogging,
):
    def __init__(
        self,
        acting_player_names: list[str],
        components: list[str] = (),
        pre_act_label: str = "\nMyComponent",
    ):
        super().__init__()
        self._acting_player_names = acting_player_names
        self._components = components
        self._pre_act_label = pre_act_label
        self._state = {"round": 0}
    
    def get_pre_act_label(self) -> str:
        return self._pre_act_label
    
    def get_pre_act_value(self) -> str:
        return f"Aktualna runda: {self._state['round']}"
    
    def pre_act(self, action_spec: entity_lib.ActionSpec) -> str:
        """Obsłuż różne typy wyjścia z silnika symulacji."""
        output_type = action_spec.output_type
        
        if output_type == entity_lib.OutputType.MAKE_OBSERVATION:
            return self._handle_make_observation(action_spec)
        elif output_type == entity_lib.OutputType.NEXT_ACTION_SPEC:
            return self._handle_next_action_spec(action_spec)
        elif output_type == entity_lib.OutputType.NEXT_ACTING:
            return self._handle_next_acting()
        elif output_type == entity_lib.OutputType.RESOLVE:
            return self._resolve(action_spec)
        elif output_type == entity_lib.OutputType.NEXT_GAME_MASTER:
            return self._handle_next_gm()
        else:
            return ""
```

### Typy Wyjścia `pre_act`

| OutputType | Cel | Co Zwrócić |
|------------|---------|----------------|
| `MAKE_OBSERVATION` | Generuj to, co widzi agent | Tekst obserwacji dla agenta |
| `NEXT_ACTION_SPEC` | Zdefiniuj format akcji agenta | String specyfikacji akcji (format JSON, itp.) |
| `NEXT_ACTING` | Określ, kto działa następny | String z nazwą agenta |
| `RESOLVE` | Rozwiąż akcje agenta | Tekst wyniku zdarzenia |
| `NEXT_GAME_MASTER` | Przekaż do innego GM | Nazwa następnego GM |

### Przykład: Obsługa Obserwacji (w stylu Marketplace)

```python
def _handle_make_observation(self, action_spec: entity_lib.ActionSpec) -> str:
    """Generuj obserwację dla obecnego agenta."""
    # Wyodrębnij nazwę agenta z action_spec
    agent_name = None
    for name in self._acting_player_names:
        if name in action_spec.call_to_action:
            agent_name = name
            break
    
    agent = self._agents[agent_name]
    
    # Zbuduj string obserwacji z aktualnym stanem agenta
    obs = (
        f"Runda: {self._state['round']+1} się rozpoczyna\n"
        f"Gotówka: {agent.cash:.2f}\n"
        f"Inwentarz {agent_name}: {agent.inventory}\n"
        "Wyślij swoją akcję."
    )
    return obs
```

### Przykład: Obsługa Specyfikacji Akcji

```python
def _handle_next_action_spec(self, agent_name: str) -> str:
    """Zdefiniuj format akcji dla agenta."""
    call_to_action = """
    Co zrobi {name}?
    Wyślij swoją decyzję jako JSON:
    {{"action":"buy","item":"ITEM_ID","qty":INTEGER}}
    """
    action_spec = entity_lib.free_action_spec(call_to_action=call_to_action)
    return engine_lib.action_spec_to_string(action_spec)
```

### Przykład: Game Master Initializera

Dla jednorazowej inicjalizacji, która przekazuje kontrolę do innego GM:

```python
class MyInitializer(entity_component.ContextComponent):
    def __init__(self,
                 model,
                 next_game_master_name: str,
                 player_names: list[str]):
        super().__init__()
        self._model = model
        self._next_game_master_name = next_game_master_name
        self._players = player_names
        self._initialized = False
    
    def pre_act(self, action_spec: entity_lib.ActionSpec) -> str:
        # Odpowiadaj tylko na zapytania NEXT_GAME_MASTER
        if action_spec.output_type != entity_lib.OutputType.NEXT_GAME_MASTER:
            return ""
        
        if self._initialized:
            # Przekaż do dialogowego/głównego GM
            return self._next_game_master_name
        
        # Uruchom logikę inicjalizacji (generuj scenę, wstrzyknij obserwacje)
        self._run_initialization()
        self._initialized = True
        
        # Zwróć własną nazwę, aby pozwolić innym komponentom zakończyć ten krok
        return self.get_entity().name
    
    def _run_initialization(self):
        """Generuj początkową scenę i wstrzyknij do kolejek obserwacji."""
        make_obs = self.get_entity().get_component("__make_observation__")
        
        scene = self._generate_scene_with_llm()
        for player in self._players:
            make_obs.add_to_queue(player, f"[Scena] {scene}")
```

### Przykład: Rozwiązywanie Akcji

```python
import json
import re

def _resolve(self, action_spec: entity_lib.ActionSpec) -> str:
    """Parsuj akcje agenta i rozwiąż wyniki."""
    # Pobierz domniemane zdarzenia z komponentów
    component_states = "\n".join(
        [self._component_pre_act_display(key) for key in self._components]
    )
    
    events = []
    for agent_name in self._acting_player_names:
        # Znajdź akcję JSON w stringu zdarzenia
        pattern = re.compile(
            rf"\b{re.escape(agent_name)}\b.*?(?P<JSON>\{{.*?\}})", re.DOTALL)
        match = pattern.search(component_states)
        
        if match:
            try:
                action = json.loads(match.group("JSON"))
                outcome = self._process_action(agent_name, action)
                events.append(outcome)
            except json.JSONDecodeError:
                events.append(f"Akcja {agent_name} nie mogła zostać sparsowana.")
    
    self._state["round"] += 1
    return "\n".join(events)
```

### Zarządzanie Stanem (Wymagane dla Checkpointingu)

```python
def get_state(self) -> entity_component.ComponentState:
    """Zwróć możliwy do serializacji stan dla checkpointingu."""
    return {
        "round": self._state["round"],
        "initialized": self._initialized,
        "agents": {
            name: dataclasses.asdict(a) for name, a in self._agents.items()
        },
    }

def set_state(self, state: entity_component.ComponentState) -> None:
    """Przywróć stan z checkpointu."""
    self._state["round"] = state.get("round", 0)
    self._initialized = state.get("initialized", False)
```

---

## Kwestionariusze

Ta sekcja obejmuje uruchamianie symulacji kwestionariuszowych: definiowanie pytań,
konfigurowanie agentów i ankietera oraz uruchamianie symulacji.

## Krok 1: Zdefiniuj Swój Kwestionariusz

### Używanie Predefiniowanego Kwestionariusza
Concordia zawiera przykładowy standardowy kwestionariusz - Depression Anxiety
Stress Scales (DASS).

```python
from concordia.contrib.data.questionnaires import depression_anxiety_stress_scale

questionnaire = depression_anxiety_stress_scale.DASSQuestionnaire()
```

### Tworzenie Własnego Kwestionariusza
Rozszerz `QuestionnaireBase` dla własnych ankiet. Zdefiniuj pytania z
`statement`, `choices` i opcjonalnym `dimension` do agregacji.

```python
from typing import Any, Dict, List
from concordia.contrib.data.questionnaires import base_questionnaire
import numpy as np
import pandas as pd

AGREEMENT_SCALE = ["Zdecydowanie się nie zgadzam", "Nie zgadzam się", "Zgadzam się", "Zdecydowanie się zgadzam"]

class CommunityWellbeingQuestionnaire(base_questionnaire.QuestionnaireBase):
  """4-pytaniowa ankieta mierząca wymiary społeczności i bezpieczeństwa."""

  def __init__(self):
    super().__init__(
        name="CommunityWellbeing",
        description="Mierzy powiązania społeczności i bezpieczeństwo.",
        questionnaire_type="multiple_choice",
        observation_preprompt="{player_name} wypełnia ankietę.",
        questions=[
            base_questionnaire.Question(
                statement="Czuję się związany ze swoją społecznością.",
                choices=AGREEMENT_SCALE,
                dimension="community",
            ),
            base_questionnaire.Question(
                statement="Moi sąsiedzi są pomocni.",
                choices=AGREEMENT_SCALE,
                dimension="community",
            ),
            base_questionnaire.Question(
                statement="Czuję się bezpiecznie w swojej okolicy.",
                choices=AGREEMENT_SCALE,
                dimension="safety",
            ),
            base_questionnaire.Question(
                statement="Ufam ludziom wokół mnie.",
                choices=AGREEMENT_SCALE,
                dimension="safety",
            ),
        ],
        dimensions=["community", "safety"],
    )

  def aggregate_results(
      self, player_answers: Dict[str, Dict[str, Any]]
  ) -> Dict[str, Any]:
    """Oblicz średni wynik dla każdego wymiaru."""
    dimension_values: Dict[str, List[float]] = {}
    for question_data in player_answers.values():
      dim = question_data["dimension"]
      val = question_data["value"]
      if val is not None:
        dimension_values.setdefault(dim, []).append(val)
    return {dim: np.mean(vals) for dim, vals in dimension_values.items()}

  def get_dimension_ranges(self) -> Dict[str, tuple[float, float]]:
    """Zakres 0-3 dla skali 4-punktowej (indeksowany 0, 1, 2, 3)."""
    return {"community": (0, 3), "safety": (0, 3)}

  def plot_results(self, results_df: pd.DataFrame, **kwargs) -> None:
    pass
```

---

## Krok 2: Skonfiguruj Encje i Ankietera

### Utwórz Instancje Encji (Agentów)

```python
persona_names = ['Alice', 'Bob', 'Charlie']

persona_instances = []
for name in persona_names:
  persona_instances.append(prefab_lib.InstanceConfig(
      prefab='basic__Entity',
      role=prefab_lib.Role.ENTITY,
      params={'name': name},
  ))
```

### Skonfiguruj Game Mastera Ankietera
Użyj `interviewer__GameMaster` dla kwestionariuszy **wielokrotnego wyboru**.

```python
interviewer_config = prefab_lib.InstanceConfig(
    prefab='interviewer__GameMaster',
    role=prefab_lib.Role.GAME_MASTER,
    params={
        'name': 'interviewer',
        'player_names': persona_names,
        'questionnaires': [questionnaire],  # Twój obiekt(y) kwestionariusza
    },
)
```

Użyj `open_ended_interviewer__GameMaster` dla kwestionariuszy **otwartych**
(wymaga embeddera).

```python
oe_interviewer_config = prefab_lib.InstanceConfig(
    prefab='open_ended_interviewer__GameMaster',
    role=prefab_lib.Role.GAME_MASTER,
    params={
        'name': 'interviewer',
        'player_names': persona_names,
        'questionnaires': [open_ended_questionnaire],
        'embedder': embedder,  # Wymagany dla pytań otwartych
    },
)
```

---

## Krok 3: Uruchom Symulację

### Zbuduj Konfigurację
Połącz prefaby i instancje w jeden obiekt `Config`.

```python
config = prefab_lib.Config(
    default_premise='',
    prefabs=prefabs,
    instances=persona_instances + [interviewer_config],  # lub oe_interviewer_config
)
```

### Utwórz Instancję i Uruchom

```python
from concordia.prefabs.simulation import questionnaire_simulation

simulation = questionnaire_simulation.QuestionnaireSimulation(
    config=config,
    model=model,
    embedder=embedder,
)

results_log = simulation.play()
```

---

### Tryby Wykonywania
Wybierz między równoległym (szybszym) lub sekwencyjnym (zależnym od kontekstu) wykonywaniem.

| Tryb | Silnik | Najlepszy Do |
|------|--------|----------|
| **Równoległy** (Domyślny) | `ParallelQuestionnaireEngine` | Szybkość, niezależne odpowiedzi |
| **Sekwencyjny** | `SequentialQuestionnaireEngine` | Odpowiedzi zależne od wcześniejszego kontekstu |

```python
from concordia.environment.engines import parallel_questionnaire

simulation = questionnaire_simulation.QuestionnaireSimulation(
    config=config,
    model=model,
    embedder=embedder,
    engine=parallel_questionnaire.ParallelQuestionnaireEngine(max_workers=4),
)
```

### Opcje Agenta: Losowanie Wyborów
Aby uniknąć błędu pozycyjnego, włącz `randomize_choices` (domyślnie: `True`) w
prefabach agentów.

```python
prefab_lib.InstanceConfig(
    prefab='basic__Entity',
    role=prefab_lib.Role.ENTITY,
    params={
        'name': 'Alice',
        'randomize_choices': False,  # Wyłącz dla deterministycznego testowania lub zachowania kolejności
    },
)
```

---

---

## Powszechne Wzorce

| Wzorzec | Implementacja |
|---------|----------------|
| Załaduj wszystkie prefaby | `helper_functions.get_package_classes(entity_prefabs)` |
| Dodaj własny prefab | `prefabs["myname__Entity"] = MyPrefab()` |
| Pobierz pamięć agenta | `entity.get_component("__memory__").get_state()` |
| Ustaw pamięć agenta | `entity.get_component("__memory__").set_state(state)` |
| Głęboka kopia do transferu | `copy.deepcopy(memory_state)` |
| Uruchom zadania równolegle | `concurrency.run_tasks(tasks_dict)` |
| Prompt akcji w wolnej formie | `entity_lib.free_action_spec(call_to_action=...)` |
| Dostęp do innych komponentów GM | `self.get_entity().get_component("__make_observation__")` |
| Dodaj obserwację do kolejki | `make_obs.add_to_queue(player_name, observation_str)` |
