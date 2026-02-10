# Concordia - Dokumentacja dla Studentów Politechniki Rzeszowskiej

## 🎓 Witamy!

To repozytorium zostało przygotowane specjalnie dla studentów Politechniki Rzeszowskiej
pracujących z robotem humanoidalnym **Unitree G1 EDU-U6**. Wszystkie dokumenty są dostępne
w języku polskim, z szczegółowymi wyjaśnieniami i praktycznymi przykładami.

## 📚 Dokumentacja Po Polsku

### Dla Początkujących - Zacznij Tutaj! 👇

1. **[README_PL.md](README_PL.md)** - Przegląd biblioteki Concordia
   - Co to jest Concordia?
   - Jak działa?
   - Jak zainstalować?
   - ⏱️ Czas czytania: 10 minut

2. **[PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md)** - Kompleksowy przewodnik dla studentów
   - Wprowadzenie krok po kroku
   - Wyjaśnienie struktury repozytorium
   - Słowniczek pojęć
   - Plan nauki (4 tygodnie)
   - FAQ
   - ⏱️ Czas czytania: 30-40 minut

3. **[examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)** - Tutorial z polskimi komentarzami
   - Interaktywny notebook Jupyter
   - Krok po kroku tworzenie pierwszej symulacji
   - Szczegółowe komentarze w języku polskim
   - ⏱️ Czas pracy: 1-2 godziny

### Dokumentacja Referencyjna 📖

4. **[CHEATSHEET_PL.md](CHEATSHEET_PL.md)** - Szybka ściągawka
   - Podstawowe koncepty
   - Najczęściej używane funkcje
   - Przykłady kodu
   - ⏱️ Odniesienie - używaj gdy czegoś potrzebujesz

5. **[PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md)** - Szczegółowy przewodnik po przykładach
   - Opis wszystkich przykładów (tutorial, dialog, alice, itp.)
   - Co każdy przykład pokazuje
   - Eksperymenty do wypróbowania
   - Zastosowania dla robota G1
   - ⏱️ Czas czytania: 45 minut

### Integracja z Robotem Unitree G1 🤖

6. **[INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md)** - Integracja Concordia z Unitree G1
   - Architektura integracji
   - Przykładowy kod middleware
   - Interfejs z robotem
   - Przetwarzanie czujników
   - Translacja akcji
   - Bezpieczeństwo
   - Troubleshooting
   - ⏱️ Czas czytania: 1-1.5 godziny

## 🗺️ Ścieżka Nauki - Krok po Kroku

### Tydzień 1: Podstawy Concordia

#### Dzień 1-2: Pierwsze Kroki
1. Przeczytaj [README_PL.md](README_PL.md)
2. Zainstaluj Concordia (instrukcje w README)
3. Uruchom [examples/tutorial_PL.ipynb](examples/tutorial_PL.ipynb)
4. **Cel:** Zrozumieć co to jest Concordia i uruchomić pierwszą symulację

#### Dzień 3-4: Głębsze Zrozumienie
1. Przeczytaj [PRZEWODNIK_STUDENTA.md](PRZEWODNIK_STUDENTA.md) sekcje 1-3
2. Eksperymentuj z tutorial - zmień parametry
3. Przejrzyj [CHEATSHEET_PL.md](CHEATSHEET_PL.md)
4. **Cel:** Zrozumieć podstawowe koncepty (Agent, Game Master, Components)

#### Dzień 5: Przykłady
1. Przeczytaj [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md) - przykłady 1-2
2. Uruchom `examples/dialog.ipynb`
3. Spróbuj stworzyć własną prostą konwersację
4. **Cel:** Nauczyć się tworzyć interakcje między agentami

### Tydzień 2: Zaawansowane Funkcje

#### Dzień 1-2: Złożone Symulacje
1. Przeczytaj [PRZEWODNIK_PRZYKLADOW.md](PRZEWODNIK_PRZYKLADOW.md) - przykłady 3-4
2. Uruchom `examples/alice.ipynb` LUB `examples/selling_cookies.ipynb`
3. Zmodyfikuj przykład - dodaj nowego agenta
4. **Cel:** Zrozumieć lokacje, sceny i złożone interakcje

#### Dzień 3-4: Projekt
1. Zaprojektuj własny scenariusz symulacji
2. Zaimplementuj go używając poznanych konceptów
3. Przetestuj i udokumentuj
4. **Cel:** Samodzielnie stworzyć działającą symulację

#### Dzień 5: Review
1. Przejrzyj swoje notatki
2. Co było trudne? Co jest jeszcze niejasne?
3. Przygotuj pytania dla wykładowcy
4. **Cel:** Utrwalić wiedzę

### Tydzień 3: Integracja z Robotem G1

#### Dzień 1-2: Teoria Integracji
1. Przeczytaj [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) sekcje 1-2
2. Zrozum architekturę: Concordia ↔ Middleware ↔ Robot
3. Przeanalizuj przykładowy kod interfejsu
4. **Cel:** Zrozumieć jak połączyć Concordia z robotem

#### Dzień 3-4: Middleware
1. Przeczytaj [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) sekcja 2-3
2. Zacznij implementować podstawowy middleware
3. Przetestuj z symulowanym robotem (MockRobot)
4. **Cel:** Stworzyć warstwę translacji między Concordia a robotem

#### Dzień 5: Agent Robota
1. Przeczytaj [INTEGRACJA_ROBOT.md](INTEGRACJA_ROBOT.md) sekcja 3
2. Zaprojektuj agenta reprezentującego robota G1
3. Zdefiniuj jego komponenty i zachowanie
4. **Cel:** Stworzyć "umysł" robota w Concordia

### Tydzień 4: Praktyczna Implementacja

#### Dzień 1: Scenariusz
1. Wybierz konkretny scenariusz dla robota (np. robot-przewodnik)
2. Zaprojektuj szczegółowo:
   - Jakie komponenty agent potrzebuje?
   - Jakie czujniki będą używane?
   - Jakie akcje robot może wykonywać?
3. **Cel:** Mieć jasny plan implementacji

#### Dzień 2-3: Implementacja
1. Implementuj scenariusz w Concordia
2. Przetestuj z symulowanym robotem
3. Iteruj i poprawiaj
4. **Cel:** Działająca symulacja gotowa do wdrożenia

#### Dzień 4: Testy
1. Testy jednostkowe komponentów
2. Testy integracyjne całego systemu
3. Testy bezpieczeństwa
4. **Cel:** Zweryfikować że system działa poprawnie i bezpiecznie

#### Dzień 5: Dokumentacja
1. Udokumentuj swoją implementację
2. Przygotuj prezentację
3. Zapisz wnioski i doświadczenia
4. **Cel:** Ukończyć projekt z pełną dokumentacją

## 📊 Poziomy Zaawansowania

### 🟢 Poziom 1: Początkujący
**Wymagania:**
- ✅ Uruchomiłeś tutorial
- ✅ Rozumiesz co to są: Agent, Game Master, Prefab
- ✅ Potrafisz stworzyć prostego agenta

**Dokumenty do przeczytania:**
- README_PL.md
- PRZEWODNIK_STUDENTA.md (sekcje 1-2)
- examples/tutorial_PL.ipynb
- PRZEWODNIK_PRZYKLADOW.md (przykłady 1-2)

### 🟡 Poziom 2: Średniozaawansowany
**Wymagania:**
- ✅ Wszystko z poziomu 1
- ✅ Uruchomiłeś przynajmniej 3 przykłady
- ✅ Rozumiesz: Components, Memory, Scenes
- ✅ Stworzyłeś własną symulację

**Dokumenty do przeczytania:**
- CHEATSHEET_PL.md (cały dokument)
- PRZEWODNIK_PRZYKLADOW.md (przykłady 3-5)
- PRZEWODNIK_STUDENTA.md (sekcja 3)

### 🔴 Poziom 3: Zaawansowany
**Wymagania:**
- ✅ Wszystko z poziomu 2
- ✅ Rozumiesz architekturę integracji z robotem
- ✅ Potrafisz zaprojektować middleware
- ✅ Stworzyłeś plan integracji z G1

**Dokumenty do przeczytania:**
- INTEGRACJA_ROBOT.md (cały dokument)
- PRZEWODNIK_PRZYKLADOW.md (przykłady 6-7)

### 🟣 Poziom 4: Expert - Gotowy do Projektu
**Wymagania:**
- ✅ Wszystko z poziomu 3
- ✅ Zaimplementowałeś i przetestowałeś middleware
- ✅ Masz działającą symulację dla konkretnego scenariusza
- ✅ Gotowy do wdrożenia na prawdziwym robocie

## 🎯 Praktyczne Scenariusze dla Robota G1

### Scenariusz 1: Robot-Przewodnik (Łatwy)
**Opis:** Robot oprowadza gości po Politechnice Rzeszowskiej.

**Co musisz umieć:**
- Poziom 2 (Średniozaawansowany)
- Dialog między robotem a gośćmi
- Lokacje (różne sale i pomieszczenia)
- Proste akcje (mówienie, poruszanie się)

**Dokumenty:** 
- PRZEWODNIK_PRZYKLADOW.md (przykład 2: dialog, przykład 3: alice)
- INTEGRACJA_ROBOT.md (sekcja: Scenariusz 1)

### Scenariusz 2: Robot-Asystent Laboratoryjny (Średni)
**Opis:** Robot pomaga w laboratorium, przynosi narzędzia, odpowiada na pytania.

**Co musisz umieć:**
- Poziom 3 (Zaawansowany)
- Zarządzanie zasobami (inwentarz)
- Manipulacja obiektami
- Rozpoznawanie obiektów z kamery

**Dokumenty:**
- PRZEWODNIK_PRZYKLADOW.md (przykład 6: marketplace)
- INTEGRACJA_ROBOT.md (sekcja: Scenariusz 2)

### Scenariusz 3: Robot w Interakcji Społecznej (Trudny)
**Opis:** Robot prowadzi naturalne konwersacje, rozpoznaje emocje, reaguje kontekstowo.

**Co musisz umieć:**
- Poziom 4 (Expert)
- Zaawansowana analiza języka naturalnego
- Rozpoznawanie emocji
- Długoterminowa pamięć interakcji
- Adaptacja zachowania

**Dokumenty:**
- PRZEWODNIK_PRZYKLADOW.md (przykład 5: actor_development)
- INTEGRACJA_ROBOT.md (sekcja: Scenariusz 3)

## 🆘 Pomoc i Wsparcie

### Mam Problem - Co Robić?

#### Krok 1: Sprawdź Dokumentację
- **Błąd instalacji?** → README_PL.md sekcja "Instalacja"
- **Nie rozumiem konceptu?** → PRZEWODNIK_STUDENTA.md sekcja "Słowniczek"
- **Przykład nie działa?** → PRZEWODNIK_PRZYKLADOW.md "Typowe Problemy"
- **Problem z robotem?** → INTEGRACJA_ROBOT.md "Troubleshooting"

#### Krok 2: FAQ
Każdy dokument ma sekcję FAQ (Często Zadawane Pytania):
- [PRZEWODNIK_STUDENTA.md - FAQ](PRZEWODNIK_STUDENTA.md#często-zadawane-pytania-faq)
- [INTEGRACJA_ROBOT.md - Troubleshooting](INTEGRACJA_ROBOT.md#troubleshooting)

#### Krok 3: Poproś o Pomoc
- Wykładowca podczas laboratorium
- Współstudenci (praca zespołowa!)
- GitHub Issues (dla problemów technicznych)

## 📖 Dokumentacja Angielska (Oryginalna)

Oprócz polskiej dokumentacji, dostępne są oryginalne dokumenty w języku angielskim:

- [README.md](README.md) - Oryginalne README
- [CHEATSHEET.md](CHEATSHEET.md) - Oryginalna ściągawka
- [examples/tutorial.ipynb](examples/tutorial.ipynb) - Oryginalny tutorial

## 🔗 Linki Szybkiego Dostępu

### Dla Początkujących
- 📖 [Zacznij tutaj: README_PL](README_PL.md)
- 🎓 [Przewodnik Studenta](PRZEWODNIK_STUDENTA.md)
- 💻 [Tutorial PL](examples/tutorial_PL.ipynb)

### Dokumentacja Referencyjna
- ⚡ [Ściągawka](CHEATSHEET_PL.md)
- 📚 [Przewodnik po Przykładach](PRZEWODNIK_PRZYKLADOW.md)

### Dla Zaawansowanych
- 🤖 [Integracja z Robotem G1](INTEGRACJA_ROBOT.md)

## ✅ Checklist Projektu

Użyj tego aby śledzić postępy w projekcie:

### Faza 1: Nauka Concordia
- [ ] Przeczytałem README_PL.md
- [ ] Przeszedłem tutorial_PL.ipynb
- [ ] Uruchomiłem przynajmniej 3 przykłady
- [ ] Stworzyłem własną prostą symulację
- [ ] Rozumiem wszystkie podstawowe koncepty

### Faza 2: Planowanie Integracji z G1
- [ ] Przeczytałem INTEGRACJA_ROBOT.md
- [ ] Zrozumiałem architekturę integracji
- [ ] Wybrałem scenariusz dla robota
- [ ] Zaprojektowałem agenta robota
- [ ] Zidentyfikowałem potrzebne komponenty

### Faza 3: Implementacja
- [ ] Zaimplementowałem agenta w Concordia
- [ ] Stworzyłem symulację testową
- [ ] Przetestowałem różne scenariusze
- [ ] Napisałem testy jednostkowe
- [ ] Udokumentowałem kod

### Faza 4: Integracja (Zaawansowana)
- [ ] Zaimplementowałem middleware
- [ ] Przetestowałem z symulowanym robotem
- [ ] Wdrożyłem na prawdziwym robocie (jeśli dostępny)
- [ ] Przeprowadziłem testy bezpieczeństwa
- [ ] Udokumentowałem wszystko

### Faza 5: Prezentacja
- [ ] Przygotowałem dokumentację końcową
- [ ] Stworzyłem prezentację
- [ ] Przygotowałem demo
- [ ] Zapisałem wnioski i doświadczenia

## 🎉 Gratulacje!

Jesteś gotowy do rozpoczęcia nauki Concordia i pracy z robotem Unitree G1 EDU-U6!

**Pamiętaj:**
- 📚 Ucz się krok po kroku
- 🔬 Eksperymentuj i testuj
- 🤝 Współpracuj z innymi
- 📝 Dokumentuj swoje postępy
- ❓ Nie bój się pytać

**Powodzenia w projekcie!** 🤖🚀

---

**Ostatnia aktualizacja:** 2026-02-10  
**Przygotowane dla:** Studenci Politechniki Rzeszowskiej  
**Kontakt:** Wykładowca prowadzący zajęcia
