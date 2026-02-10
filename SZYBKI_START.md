# Szybki Start - Quick Start Guide

## 🚀 Dla Studentów Politechniki Rzeszowskiej

Witaj! Ten dokument pomoże Ci **natychmiast** rozpocząć pracę z Concordia i robotem Unitree G1.

## ⏱️ 5 Minut do Pierwszych Kroków

### Krok 1: Przeczytaj Ten Plik (2 minuty)
📍 **Jesteś tutaj** - ten dokument da Ci podstawową orientację.

### Krok 2: Zobacz Główny Indeks (3 minuty)
📖 **Przejdź do:** [INDEX_PL.md](INDEX_PL.md)

To jest **punkt startowy** - znajdziesz tam:
- Mapę wszystkich zasobów
- Ścieżki nauki
- Linki do wszystkiego czego potrzebujesz

## 📚 Trzy Główne Ścieżki

### 🟢 Ścieżka A: Nigdy Nie Używałem Concordia
**Czas:** 2-3 dni intensywnej nauki

1. Dzień 1: [README_PL.md](README_PL.md) → [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md) (sekcje 1-2)
2. Dzień 2: [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb) - uruchom i eksperymentuj
3. Dzień 3: [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md) (przykłady 1-2)

**Efekt:** Zrozumiesz podstawy i stworzysz pierwszą symulację.

### 🟡 Ścieżka B: Znam Podstawy, Chcę Robot G1
**Czas:** 1-2 tygodnie

1. Tydzień 1: [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md) (wszystkie przykłady)
2. Tydzień 2: [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) (sekcje 1-3)

**Efekt:** Zrozumiesz jak zintegrować Concordia z robotem G1.

### 🔴 Ścieżka C: Zaawansowany - Chcę Implementować
**Czas:** 2-3 tygodnie

1. Przeczytaj: [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) (kompletny dokument)
2. Implementuj: Middleware dla robota G1
3. Testuj: Symulacje i integracja

**Efekt:** Gotowa implementacja do wdrożenia na robocie.

## 🎯 Co Chcesz Osiągnąć?

### "Chcę zrozumieć co to Concordia"
→ Zacznij tutaj: [README_PL.md](README_PL.md)
⏱️ 10 minut czytania

### "Chcę uruchomić pierwszą symulację"
→ Zacznij tutaj: [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)
⏱️ 1-2 godziny pracy

### "Chcę nauczyć się systematycznie"
→ Zacznij tutaj: [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md)
⏱️ 30-40 minut czytania + 4 tygodnie praktyki

### "Chcę zintegrować z robotem G1"
→ Zacznij tutaj: [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md)
⏱️ 1-1.5 godziny czytania + 2-3 tygodnie implementacji

### "Potrzebuję szybkiego odniesienia"
→ Zacznij tutaj: [CHEATSHEET_PL.md](CHEATSHEET_PL.md)
⏱️ Używaj gdy czegoś szukasz

### "Chcę zobaczyć przykłady"
→ Zacznij tutaj: [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md)
⏱️ 45 minut czytania + eksperymenty

## 🛠️ Instalacja - Pierwsze Kroki Techniczne

### Opcja 1: Google Colab (Najszybsza)
1. Otwórz [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)
2. Kliknij "Open in Colab"
3. Uruchom komórki od góry do dołu

**Czas:** 5 minut
**Zaleta:** Nie trzeba nic instalować lokalnie

### Opcja 2: Lokalna Instalacja
```bash
# 1. Sklonuj repozytorium
git clone https://github.com/AI-robot-lab/fork-deepmind-concordia.git
cd fork-deepmind-concordia

# 2. Utwórz środowisko wirtualne
python -m venv venv
source venv/bin/activate  # Linux/Mac
# lub
venv\Scripts\activate  # Windows

# 3. Zainstaluj Concordia
pip install --editable .[dev]

# 4. Zainstaluj zależności do przykładów
pip install --requirement=examples/requirements.in

# 5. Uruchom testy aby sprawdzić instalację
pytest --pyargs concordia
```

**Czas:** 15-20 minut
**Zaleta:** Pełna kontrola, możliwość modyfikacji

## 📋 Checklist Pierwszego Dnia

- [ ] Przeczytałem SZYBKI_START.md (ten dokument)
- [ ] Przejrzałem [INDEX_PL.md](INDEX_PL.md)
- [ ] Wybrałem swoją ścieżkę nauki (A, B, lub C)
- [ ] Zainstalowałem Concordia (Colab lub lokalnie)
- [ ] Uruchomiłem [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)
- [ ] Zrozumiałem podstawowe koncepty (Agent, Game Master)
- [ ] Wiem gdzie szukać pomocy (sekcja poniżej)

## ❓ Gdzie Szukać Pomocy

### Problem z Instalacją?
→ [README_PL.md](README_PL.md) sekcja "Instalacja"

### Nie Rozumiem Konceptu?
→ [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md) sekcja "Słowniczek"

### Przykład Nie Działa?
→ [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md) - każdy przykład ma sekcję "Typowe Problemy"

### Problem z Robotem G1?
→ [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) sekcja "Troubleshooting"

### Ogólne Pytania?
→ [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md) sekcja "FAQ"

### Coś Innego?
- Zapytaj wykładowcę
- Poproś kolegów z grupy
- Stwórz issue na GitHub

## 🎓 Najważniejsze Zasady

### 1. Ucz się Krok po Kroku
Nie przeskakuj rozdziałów. Każda sekcja buduje na poprzedniej.

### 2. Eksperymentuj
Modyfikuj przykłady. Łam rzeczy. Naprawiaj. Tak się uczysz.

### 3. Zadawaj Pytania
Nie ma głupich pytań. Jeśli czegoś nie rozumiesz, pytaj.

### 4. Dokumentuj
Rób notatki. Zapisuj co działa, co nie. To przyśpieszy naukę.

### 5. Współpracuj
Pracuj z innymi studentami. Wyjaśnianie innym utrwala wiedzę.

## 📞 Kontakt

Jeśli masz pytania:
- **Wykładowca:** Podczas zajęć laboratoryjnych
- **Zespół:** Współpraca grupowa
- **GitHub:** Issues w repozytorium

## 🎉 Gotowy?

### Twój Pierwszy Krok Zależy od Ciebie:

**Jestem początkujący:**
→ [README_PL.md](README_PL.md)

**Chcę praktyki:**
→ [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)

**Potrzebuję planu:**
→ [INDEX_PL.md](INDEX_PL.md)

**Chcę robot:**
→ [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md)

## 🚀 Czas Zacząć!

Wybierz link powyżej i zacznij swoją przygodę z Concordia i robotem Unitree G1!

---

**Pamiętaj:** INDEX_PL.md to Twój główny punkt nawigacji. Zawsze możesz tam wrócić.

**Powodzenia!** 🤖📚🎓
