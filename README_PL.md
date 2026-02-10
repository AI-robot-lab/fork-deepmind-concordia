# Concordia

*Biblioteka do generatywnej symulacji społecznej*

<!-- GITHUB -->
<!-- disableFinding(LINK_RELATIVE_G3DOC) -->
[![Python](https://img.shields.io/pypi/pyversions/gdm-concordia.svg)](https://pypi.python.org/pypi/gdm-concordia)
[![PyPI version](https://img.shields.io/pypi/v/gdm-concordia.svg)](https://pypi.python.org/pypi/gdm-concordia)
[![PyPI tests](../../actions/workflows/pypi-test.yml/badge.svg)](../../actions/workflows/pypi-test.yml)
[![Tests](../../actions/workflows/test-concordia.yml/badge.svg)](../../actions/workflows/test-concordia.yml)
[![Examples](../../actions/workflows/test-examples.yml/badge.svg)](../../actions/workflows/test-examples.yml)
<!-- /GITHUB -->

[Raport Techniczny Concordia](https://arxiv.org/abs/2312.03664) | [Wzorzec Projektowy Concordia](https://arxiv.org/abs/2507.08892) | [Ściągawka Kodu](CHEATSHEET.md) | [Wersja Angielska](README.md)

## O Bibliotece

Concordia to biblioteka ułatwiająca konstruowanie i wykorzystanie generatywnych modeli
opartych na agentach (agent-based models) do symulacji interakcji agentów w ugruntowanej
fizycznej, społecznej lub cyfrowej przestrzeni. Umożliwia łatwe i elastyczne definiowanie
środowisk przy użyciu wzorca interakcji zapożyczonego z gier fabularnych RPG, w którym
specjalny agent zwany Mistrzem Gry (Game Master - GM) jest odpowiedzialny za symulację
środowiska, w którym wchodzą w interakcje agenci gracze (podobnie jak narrator w
interaktywnej opowieści). Agenci podejmują działania opisując w naturalnym języku to, co
chcą zrobić. Mistrz Gry następnie tłumaczy ich działania na odpowiednie implementacje.
W symulowanym świecie fizycznym, GM sprawdzałby fizyczną prawdopodobność działań agentów
i opisywał ich efekty. W środowiskach cyfrowych symulujących technologie takie jak aplikacje
i usługi, GM może, na podstawie danych wejściowych agenta, obsługiwać niezbędne wywołania
API do integracji z zewnętrznymi narzędziami.

Concordia wspiera szeroką gamę zastosowań, od badań nauk społecznych i etyki AI po
kognitywną neuronaukę i ekonomię; dodatkowo może być również wykorzystywana do
generowania danych dla aplikacji personalizacyjnych oraz do przeprowadzania ocen
wydajności rzeczywistych usług poprzez symulowane użycie.

Concordia wymaga dostępu do standardowego API modelu językowego (LLM), i opcjonalnie
może również integrować się z rzeczywistymi aplikacjami i usługami.

## Jak to Działa

Concordia działa jak **Silnik Gry** dla generatywnych agentów.

*   **Encje (Entities)**: Aktorzy w symulacji. Mogą to być postacie graczy (Agents)
    lub kontrolery systemowe (Game Masters).
*   **Komponenty (Components)**: Bloki konstrukcyjne Encji. Podobnie jak obiekt gry
    może mieć komponent "współrzędne", agent Concordia ma komponenty dla
    **Pamięci**, **Obserwacji**, **Planowania** i **Działania**.
*   **Silnik (Engine)**: Pętla napędzająca symulację. Pyta agentów o działania
    i prosi Mistrza Gry o ich rozwiązanie.

Ta modularna architektura pozwala na składanie złożonych zachowań z prostych,
wielokrotnego użytku części.

## Struktura Folderów

*   **[`concordia/prefabs`](concordia/prefabs/README.md)**: Wstępnie złożone
    przepisy dla typowych agentów i Mistrzów Gry.
*   **[`concordia/components`](concordia/components/README.md)**: Modułowe
    bloki konstrukcyjne dla agentów, w tym systemy pamięci, łańcuchy rozumowania
    i moduły sensoryczne.
*   **[`concordia/environment`](concordia/environment/README.md)**: "Silnik"
    symulacji, zawierający Mistrza Gry i pętlę kolejkowania.
*   **[`concordia/thought_chains`](concordia/thought_chains/README.md)**: Logika
    dla wewnętrznych kroków rozumowania (np. Chain of Thought).
*   **[`concordia/document`](concordia/document/README.md)**: Narzędzia do
    zarządzania promptami LLM i kontekstem.
*   **[`concordia/language_model`](concordia/language_model/README.md)**: Integracja
    LLM i wrappery API.
*   **[`examples/`](examples/)**: Tutoriale i przykładowe symulacje, które pomogą
    Ci zacząć.

> [!TIP]
> Najlepszym sposobem na naukę jest obejrzenie tutoriala
> [Concordia: Building Generative Agent-Based Models](https://youtu.be/2FO5g65mu2I?si=TSk7XTk4gCaadEDs)
> na YouTube, uruchomienie **[`examples/tutorial.ipynb`](examples/tutorial.ipynb)**
> a następnie próba modyfikacji **Prefabs**, aby zobaczyć jak zmienia się zachowanie agenta.

## Instalacja

[Concordia jest dostępna na PyPI](https://pypi.python.org/pypi/gdm-concordia)
i może być zainstalowana używając:

```shell
pip install gdm-concordia
```

Po tym możesz zaimportować concordia w swoim kodzie używając `import concordia`.

## Rozwój

### Codespace

Najprostszym sposobem na pracę z kodem źródłowym Concordia jest użycie naszego
prekonfigurowanego środowiska programistycznego poprzez
[Github CodeSpace](https://github.com/features/codespaces).

Zapewnia to przetestowany przepływ pracy programistycznej, który pozwala na
odtwarzalne buildy i minimalizuje zarządzanie zależnościami. Zdecydowanie zalecamy
przygotowanie wszystkich Pull Requestów dla Concordia poprzez ten przepływ pracy.

### Konfiguracja ręczna

Jeśli chcesz pracować z kodem źródłowym Concordia w swoim własnym środowisku
programistycznym, będziesz musiał samodzielnie obsłużyć instalację i zarządzanie
zależnościami.

Na przykład, możesz wykonać edytowalną instalację w następujący sposób:

1.  Sklonuj Concordia:

    ```shell
    git clone -b main https://github.com/google-deepmind/concordia
    cd concordia
    ```

2.  Utwórz i aktywuj wirtualne środowisko:

    ```shell
    python -m venv venv
    source venv/bin/activate
    ```

3.  Zainstaluj Concordia:

    ```shell
    pip install --editable .[dev]
    ```

4.  Przetestuj instalację:

    ```shell
    pytest --pyargs concordia
    ```

5.  Zainstaluj wszelkie dodatkowe zależności modelu językowego, których będziesz
    potrzebować, np.:

    ```shell
    pip install .[google]
    pip install --requirement=examples/requirements.in
    ```

    Zauważ, że na tym etapie możesz odkryć, że Twoje środowisko programistyczne nie
    jest wspierane przez niektóre bazowe zależności i będziesz musiał wykonać pewne
    zarządzanie zależnościami.

## Własny Model Językowy (LLM)

Concordia wymaga dostępu do API modelu językowego (LLM). Każde API LLM wspierające
próbkowanie tekstu powinno działać. Jakość wyników zależy od wybranego LLM. Niektóre
modele lepiej radzą sobie z odgrywaniem ról niż inne. Musisz również dostarczyć
embedder tekstu dla pamięci asocjacyjnej. Każde osadzanie o stałych wymiarach działa
w tym przypadku. Idealnie byłoby to takie, które dobrze działa dla podobieństwa zdań
lub wyszukiwania semantycznego.

## Przykładowe użycie

Poniżej znajdziesz ilustracyjną symulację społeczną, w której 4 przyjaciół jest
uwięzionych w zaśnieżonym pubie. Dwaj z nich mają spór dotyczący rozbitego samochodu.

Agenci są zbudowani przy użyciu prostego rozumowania zainspirowanego przez March i
Olsen (2011), którzy postulują, że ludzie generalnie działają tak, jakby wybierali
swoje działania odpowiadając na trzy kluczowe pytania:

1. Jaką jest to sytuacja?
2. Kim jestem?
3. Co osoba taka jak ja robi w sytuacji takiej jak ta?

Agenci użyci w poniższym przykładzie implementują dokładnie te pytania:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.sandbox.google.com/github/google-deepmind/concordia/blob/main/examples/tutorial.ipynb)

## Cytowanie Concordia

Jeśli używasz Concordia w swojej pracy, prosimy o cytowanie towarzyszącego artykułu:

<!-- disableFinding(SNIPPET_INVALID_LANGUAGE) -->
```bibtex
@article{vezhnevets2023generative,
  title={Generative agent-based modeling with actions grounded in physical,
  social, or digital space using Concordia},
  author={Vezhnevets, Alexander Sasha and Agapiou, John P and Aharon, Avia and
  Ziv, Ron and Matyas, Jayd and Du{\'e}{\~n}ez-Guzm{\'a}n, Edgar A and
  Cunningham, William A and Osindero, Simon and Karmon, Danny and
  Leibo, Joel Z},
  journal={arXiv preprint arXiv:2312.03664},
  year={2023}
}
```

## Zastrzeżenie

To nie jest oficjalnie wspierany produkt Google.
