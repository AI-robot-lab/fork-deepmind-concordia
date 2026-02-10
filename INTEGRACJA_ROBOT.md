# Integracja Concordia z Robotem Unitree G1 EDU-U6

## Wprowadzenie

Ten dokument opisuje jak zintegrować symulacje Concordia z fizycznym robotem
humanoidalnym Unitree G1 EDU-U6. Robot G1 to zaawansowana platforma humanoidalna
z możliwościami manipulacji, lokomocji i interakcji z otoczeniem.

## Architektura Integracji

### Warstwa 1: Concordia (Mózg - Warstwa Decyzyjna)
**Co robi:** Podejmuje inteligentne decyzje na podstawie obserwacji otoczenia.

**Komponenty:**
- Agent reprezentujący "umysł" robota
- Game Master przetwarzający informacje z czujników
- Komponenty pamięci, planowania i działania

**Przykład:**
```
Agent → "Widzę osobę, powinienem się przywitać" → Decyzja: "Powiedz: Witaj!"
```

### Warstwa 2: Middleware (Most - Warstwa Translacji)
**Co robi:** Tłumaczy między abstrakcyjnymi decyzjami a konkretnymi komendami robota.

**Główne zadania:**
- Konwersja danych z czujników na obserwacje zrozumiałe dla Concordia
- Translacja decyzji Concordia na konkretne komendy API robota
- Synchronizacja czasowa
- Obsługa błędów i sytuacji awaryjnych

**Przykład:**
```
Concordia: "Powiedz: Witaj!" → Middleware → G1 API: play_audio("witaj.mp3") + gesture("wave")
```

### Warstwa 3: Unitree G1 API (Ciało - Warstwa Fizyczna)
**Co robi:** Bezpośrednia kontrola nad robotem.

**Możliwości:**
- Sterowanie stawami (joint control)
- Czytanie danych z czujników (IMU, encodery, kamery)
- Kontrola głosowa (text-to-speech)
- Wykrywanie kolizji
- Zarządzanie energią

## Schemat Komunikacji

```
┌─────────────────────────────────────────────────────────────┐
│                     CONCORDIA (Python)                       │
│  ┌────────────┐  ┌──────────────┐  ┌─────────────────┐    │
│  │   Agent    │  │ Game Master  │  │   Environment   │    │
│  │  (Robot)   │  │ (Interpreter)│  │   (World)       │    │
│  └─────┬──────┘  └──────┬───────┘  └────────┬────────┘    │
│        │                 │                    │              │
│        └─────────────────┼────────────────────┘              │
│                          │                                   │
└──────────────────────────┼───────────────────────────────────┘
                           │
                           │ Abstrakcyjne decyzje i obserwacje
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                    MIDDLEWARE (Python)                        │
│  ┌──────────────────────────────────────────────────────┐   │
│  │           Sensor Data Processor                       │   │
│  │  Kamery → Detekcja obiektów → Obserwacje Concordia   │   │
│  │  IMU → Orientacja → Stan równowagi                    │   │
│  │  Mikrofon → Speech-to-Text → Intencje użytkownika    │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │           Action Translator                           │   │
│  │  "Powiedz X" → TTS + kontrola ust                     │   │
│  │  "Idź naprzód" → Sekwencja kroków                     │   │
│  │  "Podnieś obiekt" → Trajektoria ramienia              │   │
│  └──────────────────────────────────────────────────────┘   │
└──────────────────────────┬───────────────────────────────────┘
                           │
                           │ Konkretne komendy i dane z czujników
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                  UNITREE G1 API (C++/Python)                 │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │  Kontrola   │  │  Czujniki    │  │    Audio        │   │
│  │  Stawów     │  │  (Kamery,    │  │  (Mikrofon,     │   │
│  │             │  │   IMU, etc)  │  │   Głośniki)     │   │
│  └──────┬──────┘  └──────┬───────┘  └────────┬────────┘   │
│         │                 │                    │             │
└─────────┼─────────────────┼────────────────────┼─────────────┘
          │                 │                    │
┌─────────▼─────────────────▼────────────────────▼─────────────┐
│              UNITREE G1 EDU-U6 (Hardware)                    │
│         Silniki, Czujniki, Aktuatory, Elektronika            │
└───────────────────────────────────────────────────────────────┘
```

## Implementacja Krok po Kroku

### Krok 1: Przygotowanie Środowiska

#### 1.1 Instalacja Concordia
```bash
# Klonowanie repozytorium
git clone https://github.com/google-deepmind/concordia.git
cd concordia

# Tworzenie wirtualnego środowiska
python -m venv venv_concordia
source venv_concordia/bin/activate  # Linux/Mac
# lub
venv_concordia\Scripts\activate  # Windows

# Instalacja
pip install --editable .[dev]
```

#### 1.2 Instalacja SDK Unitree G1
```bash
# Pobranie SDK Unitree (przykładowa ścieżka - sprawdź dokumentację Unitree)
git clone https://github.com/unitreerobotics/unitree_sdk2_python.git
cd unitree_sdk2_python
pip install -e .
```

#### 1.3 Instalacja Dodatkowych Zależności
```bash
# Przetwarzanie obrazów i dźwięku
pip install opencv-python
pip install pyaudio
pip install sounddevice
pip install pyttsx3  # Text-to-Speech
pip install speech_recognition  # Speech-to-Text

# Obsługa komunikacji
pip install websockets
pip install asyncio
```

### Krok 2: Implementacja Middleware

#### 2.1 Struktura Projektu
```
robot_integration/
├── concordia_agent/
│   ├── __init__.py
│   ├── robot_agent.py      # Definicja agenta robota
│   ├── robot_components.py  # Niestandardowe komponenty
│   └── robot_environment.py # Środowisko dla robota
├── middleware/
│   ├── __init__.py
│   ├── sensor_processor.py  # Przetwarzanie danych z czujników
│   ├── action_translator.py # Translacja akcji na komendy
│   ├── g1_interface.py      # Interfejs z API Unitree
│   └── safety_monitor.py    # Monitor bezpieczeństwa
├── config/
│   ├── robot_config.yaml    # Konfiguracja robota
│   └── concordia_config.yaml # Konfiguracja Concordia
├── tests/
│   ├── test_integration.py
│   └── test_safety.py
└── main.py                  # Główny program
```

#### 2.2 Przykładowy Kod: Interfejs z G1

```python
# middleware/g1_interface.py

"""
Interfejs do komunikacji z robotem Unitree G1.
Ten moduł zapewnia abstrakcję nad niskopoziomowym API robota.
"""

import unitree_sdk2py  # SDK Unitree
import numpy as np
from typing import Dict, List, Optional
import logging

# Konfiguracja loggera dla śledzenia działania
logger = logging.getLogger(__name__)


class UnitreeG1Interface:
    """
    Klasa obsługująca komunikację z robotem Unitree G1 EDU-U6.
    
    Główne zadania:
    1. Inicjalizacja połączenia z robotem
    2. Wysyłanie komend ruchu i akcji
    3. Odbieranie danych z czujników
    4. Monitorowanie stanu robota
    """
    
    def __init__(self, robot_ip: str = "192.168.123.10"):
        """
        Inicjalizacja interfejsu robota.
        
        Args:
            robot_ip: Adres IP robota w sieci lokalnej
        
        Dlaczego potrzebujemy IP?
        Robot komunikuje się przez sieć, potrzebujemy znać jego adres.
        """
        logger.info(f"Inicjalizacja połączenia z robotem G1 pod adresem {robot_ip}")
        
        # Utworzenie klienta SDK
        # To jest nasz "telefon" do robota
        self.client = unitree_sdk2py.RobotClient(robot_ip)
        
        # Połączenie z robotem
        if not self.client.connect():
            raise ConnectionError(f"Nie można połączyć się z robotem pod {robot_ip}")
        
        logger.info("Połączenie z robotem nawiązane pomyślnie")
        
        # Stan robota - przechowujemy ostatnie odczyty
        self.current_state = {
            'joint_positions': None,  # Pozycje stawów
            'joint_velocities': None, # Prędkości stawów
            'imu_data': None,         # Dane z żyroskopu/akcelerometru
            'battery_level': None,    # Poziom baterii
            'camera_frame': None      # Obraz z kamery
        }
        
    def get_sensor_data(self) -> Dict:
        """
        Pobiera aktualne dane ze wszystkich czujników robota.
        
        Returns:
            Słownik z danymi z czujników
            
        Dlaczego to jest ważne?
        Agent Concordia musi "widzieć" świat - dane z czujników są jego "oczami".
        """
        logger.debug("Pobieranie danych z czujników")
        
        # Odczyt pozycji stawów
        # Każdy staw ma swoją pozycję - to mówi nam jak robot jest "złożony"
        joint_data = self.client.get_joint_states()
        self.current_state['joint_positions'] = joint_data.positions
        self.current_state['joint_velocities'] = joint_data.velocities
        
        # Odczyt IMU (Inertial Measurement Unit)
        # To mówi nam czy robot stoi prosto, czy się przewraca
        imu_data = self.client.get_imu_data()
        self.current_state['imu_data'] = {
            'orientation': imu_data.quaternion,  # Orientacja w przestrzeni
            'angular_velocity': imu_data.gyro,   # Jak szybko się obraca
            'linear_acceleration': imu_data.accel # Jak szybko przyśpiesza
        }
        
        # Odczyt poziomu baterii
        # Ważne - robot musi wiedzieć kiedy musi się naładować!
        battery = self.client.get_battery_state()
        self.current_state['battery_level'] = battery.percentage
        
        # Odczyt obrazu z kamery
        # To pozwoli robotowi "widzieć" otoczenie
        camera_frame = self.client.get_camera_frame()
        self.current_state['camera_frame'] = camera_frame
        
        return self.current_state.copy()
    
    def execute_action(self, action: Dict) -> bool:
        """
        Wykonuje akcję na robocie na podstawie decyzji agenta.
        
        Args:
            action: Słownik opisujący akcję do wykonania
                    Przykład: {'type': 'speak', 'text': 'Witaj!'}
                    Przykład: {'type': 'move', 'direction': 'forward', 'distance': 1.0}
        
        Returns:
            True jeśli akcja została wykonana pomyślnie
            
        Dlaczego to jest ważne?
        To jest "most" między abstrakcyjnymi decyzjami agenta a fizycznymi ruchami robota.
        """
        action_type = action.get('type')
        logger.info(f"Wykonywanie akcji: {action_type}")
        
        try:
            if action_type == 'speak':
                # Akcja: Robot mówi
                return self._speak(action['text'])
            
            elif action_type == 'move':
                # Akcja: Robot się porusza
                return self._move(action['direction'], action.get('distance', 1.0))
            
            elif action_type == 'gesture':
                # Akcja: Robot wykonuje gest (np. machanie ręką)
                return self._perform_gesture(action['gesture_name'])
            
            elif action_type == 'manipulate':
                # Akcja: Robot manipuluje obiektem (chwyta, podnosi)
                return self._manipulate_object(action)
            
            else:
                logger.warning(f"Nieznany typ akcji: {action_type}")
                return False
                
        except Exception as e:
            logger.error(f"Błąd podczas wykonywania akcji: {e}")
            return False
    
    def _speak(self, text: str) -> bool:
        """
        Generuje mowę z tekstu i odtwarza przez głośniki robota.
        
        Args:
            text: Tekst do wypowiedzenia
        
        Proces:
        1. Tekst → Text-to-Speech → Plik audio
        2. Plik audio → Odtwarzacz → Głośniki robota
        """
        logger.info(f"Robot mówi: {text}")
        
        # Tutaj można użyć TTS (Text-to-Speech)
        # Przykład z pyttsx3:
        # engine = pyttsx3.init()
        # engine.say(text)
        # engine.runAndWait()
        
        # Lub wysłanie do API robota jeśli ma wbudowany TTS
        return self.client.play_audio_text(text)
    
    def _move(self, direction: str, distance: float) -> bool:
        """
        Porusza robotem w określonym kierunku.
        
        Args:
            direction: 'forward', 'backward', 'left', 'right'
            distance: Dystans w metrach
        
        Jak to działa:
        1. Planowanie trajektorii kroków
        2. Obliczenie pozycji stawów dla każdego kroku
        3. Sekwencyjne wykonanie kroków
        4. Monitorowanie równowagi (IMU)
        """
        logger.info(f"Robot porusza się {direction} o {distance}m")
        
        # Konwersja kierunku na wektor przemieszczenia
        displacement = self._direction_to_vector(direction, distance)
        
        # Wysłanie komendy ruchu do robota
        return self.client.move_to_relative_position(displacement)
    
    def _perform_gesture(self, gesture_name: str) -> bool:
        """
        Wykonuje predefiniowany gest.
        
        Gestures mogą obejmować:
        - wave: Machanie ręką na powitanie
        - thumbs_up: Kciuk w górę
        - point: Wskazywanie palcem
        - bow: Ukłon
        """
        logger.info(f"Robot wykonuje gest: {gesture_name}")
        
        # Pobranie trajektorii gestu z biblioteki
        gesture_trajectory = self._get_gesture_trajectory(gesture_name)
        
        # Wykonanie trajektorii
        return self.client.execute_joint_trajectory(gesture_trajectory)
    
    def _manipulate_object(self, action: Dict) -> bool:
        """
        Manipulacja obiektami - chwytanie, przenoszenie, odkładanie.
        
        Args:
            action: Szczegóły manipulacji
                    {'action': 'grasp', 'object_position': [x, y, z]}
        """
        manipulation_type = action.get('action')
        logger.info(f"Robot manipuluje: {manipulation_type}")
        
        if manipulation_type == 'grasp':
            # Chwytanie obiektu
            target_pos = action['object_position']
            return self._grasp_object(target_pos)
        
        elif manipulation_type == 'release':
            # Puszczanie obiektu
            return self._release_object()
        
        return False
    
    def _grasp_object(self, position: List[float]) -> bool:
        """
        Sekwencja chwytania obiektu:
        1. Planowanie trajektorii ramienia do pozycji obiektu
        2. Otworzenie chwytaka
        3. Ruch do obiektu
        4. Zamknięcie chwytaka
        5. Podniesienie obiektu
        """
        logger.info(f"Chwytanie obiektu na pozycji {position}")
        
        # 1. Oblicz inverse kinematics (IK) - jakie pozycje stawów są potrzebne
        #    aby ramię dotarło do pozycji obiektu
        joint_positions = self._compute_ik(position)
        
        # 2. Otwórz chwytaki
        self.client.set_gripper_position(1.0)  # 1.0 = otwarty
        
        # 3. Przesuń ramię do pozycji
        self.client.move_arm_to_position(joint_positions)
        
        # 4. Zamknij chwytaki
        self.client.set_gripper_position(0.0)  # 0.0 = zamknięty
        
        # 5. Podnieś
        lift_position = joint_positions.copy()
        lift_position[2] += 0.1  # Podnieś o 10cm
        self.client.move_arm_to_position(lift_position)
        
        return True
    
    def _compute_ik(self, target_position: List[float]) -> np.ndarray:
        """
        Inverse Kinematics - oblicza pozycje stawów potrzebne
        aby effektor końcowy (np. dłoń) był w docelowej pozycji.
        
        To jest złożone obliczenie matematyczne wykorzystujące
        model kinematyczny robota.
        """
        # Tutaj powinna być faktyczna implementacja IK
        # Dla celów demonstracyjnych, zwracamy przykładową wartość
        return np.zeros(7)  # 7 stawów ramienia
    
    def emergency_stop(self):
        """
        ZATRZYMANIE AWARYJNE
        
        To jest funkcja bezpieczeństwa - zatrzymuje wszystkie ruchy robota
        natychmiast. Używana gdy:
        - Wykryto niebezpieczeństwo
        - Człowiek wchodzi w strefę robota
        - Błąd w oprogramowaniu
        """
        logger.critical("EMERGENCY STOP ACTIVATED")
        self.client.stop_all_motion()
        self.client.disable_motors()
    
    def _direction_to_vector(self, direction: str, distance: float) -> np.ndarray:
        """Konwertuje kierunek tekstowy na wektor przemieszczenia."""
        vectors = {
            'forward': np.array([distance, 0, 0]),
            'backward': np.array([-distance, 0, 0]),
            'left': np.array([0, distance, 0]),
            'right': np.array([0, -distance, 0])
        }
        return vectors.get(direction, np.array([0, 0, 0]))
    
    def _get_gesture_trajectory(self, gesture_name: str) -> List:
        """Zwraca predefiniowaną trajektorię dla gestu."""
        # Tutaj byłyby zapisane trajektorie dla różnych gestów
        # Na razie zwracamy pustą listę
        return []
    
    def disconnect(self):
        """Bezpieczne rozłączenie z robotem."""
        logger.info("Rozłączanie z robotem")
        self.client.disconnect()
```

#### 2.3 Przykładowy Kod: Przetwarzanie Czujników

```python
# middleware/sensor_processor.py

"""
Przetwarza surowe dane z czujników robota na obserwacje
zrozumiałe dla agenta Concordia.
"""

import numpy as np
from typing import Dict, List
import cv2
import logging

logger = logging.getLogger(__name__)


class SensorProcessor:
    """
    Przetwarza dane z czujników na wysoko-poziomowe obserwacje.
    
    Dlaczego to jest potrzebne?
    Agent Concordia operuje na poziomie abstrakcyjnym ("widzę osobę")
    a nie na poziomie surowych danych ("piksel[100,100] = [255,0,0]").
    """
    
    def __init__(self):
        """Inicjalizacja procesorów dla różnych typów czujników."""
        logger.info("Inicjalizacja SensorProcessor")
        
        # Inicjalizacja detektora obiektów dla kamery
        # Używamy np. YOLO lub inny model wykrywania obiektów
        self.object_detector = self._init_object_detector()
        
        # Inicjalizacja rozpoznawania twarzy
        self.face_detector = cv2.CascadeClassifier(
            cv2.data.haarcascades + 'haarcascade_frontalface_default.xml'
        )
        
    def process_camera(self, camera_frame: np.ndarray) -> Dict:
        """
        Przetwarza obraz z kamery na strukturyzowane obserwacje.
        
        Args:
            camera_frame: Surowy obraz z kamery (numpy array)
        
        Returns:
            Słownik z wykrytymi obiektami i informacjami
            
        Proces:
        1. Obraz surowy → Detekcja obiektów → Lista obiektów
        2. Obraz surowy → Detekcja twarzy → Lista osób
        3. Agregacja → Opis sceny dla agenta
        """
        logger.debug("Przetwarzanie obrazu z kamery")
        
        observations = {
            'people_detected': [],
            'objects_detected': [],
            'scene_description': ""
        }
        
        # Wykrywanie twarzy (ludzi)
        gray = cv2.cvtColor(camera_frame, cv2.COLOR_BGR2GRAY)
        faces = self.face_detector.detectMultiScale(
            gray, 
            scaleFactor=1.1, 
            minNeighbors=5
        )
        
        # Dla każdej wykrytej twarzy
        for (x, y, w, h) in faces:
            person_info = {
                'position': (x + w//2, y + h//2),  # Środek twarzy
                'distance': self._estimate_distance(w),  # Szacowana odległość
                'is_looking_at_robot': self._is_facing_camera(x, y, w, h, camera_frame)
            }
            observations['people_detected'].append(person_info)
        
        # Wykrywanie innych obiektów
        # (kod dla detektora obiektów - np. YOLO)
        detected_objects = self._detect_objects(camera_frame)
        observations['objects_detected'] = detected_objects
        
        # Generowanie opisu sceny w języku naturalnym
        observations['scene_description'] = self._generate_scene_description(
            observations
        )
        
        return observations
    
    def process_imu(self, imu_data: Dict) -> Dict:
        """
        Przetwarza dane z IMU (żyroskop + akcelerometr).
        
        Args:
            imu_data: Surowe dane z IMU
        
        Returns:
            Wysoko-poziomowe informacje o stanie równowagi
            
        Dlaczego to jest ważne?
        Robot musi wiedzieć czy stoi stabilnie, czy się przewraca,
        czy może bezpiecznie wykonać ruch.
        """
        logger.debug("Przetwarzanie danych IMU")
        
        # Oblicz kąt pochylenia
        orientation = imu_data['orientation']
        tilt_angle = self._quaternion_to_euler(orientation)
        
        # Sprawdź czy robot jest w bezpiecznej pozycji
        is_stable = abs(tilt_angle[0]) < 10 and abs(tilt_angle[1]) < 10  # stopnie
        
        # Wykryj czy robot pada
        acceleration = imu_data['linear_acceleration']
        is_falling = self._detect_falling(acceleration, tilt_angle)
        
        return {
            'is_stable': is_stable,
            'is_falling': is_falling,
            'tilt_angle': tilt_angle,
            'balance_status': 'stable' if is_stable else 'unstable'
        }
    
    def process_all_sensors(self, sensor_data: Dict) -> str:
        """
        Agreguje wszystkie dane z czujników w jeden opis tekstowy
        dla agenta Concordia.
        
        Args:
            sensor_data: Wszystkie surowe dane z czujników
        
        Returns:
            Tekstowy opis obserwacji dla agenta
            
        Przykładowy output:
        "Widzę 2 osoby w odległości 3 metrów. Jedna osoba patrzy na mnie.
         W pokoju znajduje się stół i krzesło. Robot stoi stabilnie."
        """
        # Przetwarzanie kamery
        camera_obs = self.process_camera(sensor_data['camera_frame'])
        
        # Przetwarzanie IMU
        imu_obs = self.process_imu(sensor_data['imu_data'])
        
        # Komponowanie opisu tekstowego
        description_parts = []
        
        # Ludzie
        num_people = len(camera_obs['people_detected'])
        if num_people > 0:
            description_parts.append(f"Widzę {num_people} osób.")
            looking_count = sum(
                1 for p in camera_obs['people_detected'] 
                if p['is_looking_at_robot']
            )
            if looking_count > 0:
                description_parts.append(f"{looking_count} osób patrzy na mnie.")
        
        # Obiekty
        if camera_obs['objects_detected']:
            objects_str = ", ".join([obj['name'] for obj in camera_obs['objects_detected']])
            description_parts.append(f"W otoczeniu znajduje się: {objects_str}.")
        
        # Stan równowagi
        description_parts.append(f"Robot {imu_obs['balance_status']}.")
        
        # Poziom baterii
        battery = sensor_data.get('battery_level', 100)
        if battery < 20:
            description_parts.append(f"UWAGA: Niski poziom baterii ({battery}%)!")
        
        return " ".join(description_parts)
    
    def _estimate_distance(self, face_width: int) -> float:
        """
        Szacuje odległość do osoby na podstawie szerokości twarzy na obrazie.
        
        Zasada: Im większa twarz na obrazie, tym bliżej osoba.
        """
        # Założenie: przeciętna szerokość twarzy to ~15cm
        # Prosta formuła: distance = (known_width * focal_length) / pixel_width
        FACE_WIDTH_CM = 15
        FOCAL_LENGTH = 500  # Zależy od kamery, wymaga kalibracji
        
        distance_cm = (FACE_WIDTH_CM * FOCAL_LENGTH) / face_width
        return distance_cm / 100  # konwersja na metry
    
    def _is_facing_camera(self, x: int, y: int, w: int, h: int, 
                          frame: np.ndarray) -> bool:
        """
        Sprawdza czy osoba patrzy w stronę kamery.
        
        Uproszczona wersja - sprawdza czy twarz jest centralna i wyraźna.
        Zaawansowana wersja mogłaby używać detekcji kierunku spojrzenia.
        """
        frame_center_x = frame.shape[1] // 2
        face_center_x = x + w // 2
        
        # Jeśli środek twarzy jest blisko środka kadru, osoba prawdopodobnie patrzy na robot
        distance_from_center = abs(face_center_x - frame_center_x)
        return distance_from_center < frame.shape[1] * 0.2  # w promieniu 20% szerokości
    
    def _detect_objects(self, frame: np.ndarray) -> List[Dict]:
        """
        Wykrywa obiekty na obrazie używając modelu detekcji obiektów.
        
        Tutaj można użyć YOLO, Faster R-CNN lub inny model.
        """
        # Przykładowa implementacja (placeholder)
        # W rzeczywistości użyłbyś modelu ML do detekcji
        detected = []
        
        # objects = self.object_detector.detect(frame)
        # for obj in objects:
        #     detected.append({
        #         'name': obj.class_name,
        #         'confidence': obj.confidence,
        #         'position': obj.bounding_box
        #     })
        
        return detected
    
    def _generate_scene_description(self, observations: Dict) -> str:
        """Generuje naturalny opis sceny na podstawie obserwacji."""
        # Można użyć prostych reguł lub modelu generującego opisy
        return "Scena z ludźmi i obiektami"
    
    def _quaternion_to_euler(self, quaternion: List[float]) -> List[float]:
        """Konwertuje kwaternion na kąty Eulera (roll, pitch, yaw)."""
        # Implementacja konwersji kwaternion → Euler
        # Uproszczona wersja
        return [0.0, 0.0, 0.0]  # [roll, pitch, yaw] w stopniach
    
    def _detect_falling(self, acceleration: List[float], tilt: List[float]) -> bool:
        """
        Wykrywa czy robot pada.
        
        Sygnały upadku:
        - Nagły wzrost przyspieszenia
        - Duży kąt pochylenia
        - Szybka zmiana orientacji
        """
        # Jeśli przyspieszenie przekracza próg (robot spada)
        accel_magnitude = np.linalg.norm(acceleration)
        if accel_magnitude > 15.0:  # m/s² (przyspieszenie większe niż grawitacja)
            return True
        
        # Jeśli kąt pochylenia jest zbyt duży
        if abs(tilt[0]) > 30 or abs(tilt[1]) > 30:  # stopnie
            return True
        
        return False
    
    def _init_object_detector(self):
        """Inicjalizacja modelu detekcji obiektów."""
        # Tutaj załadowałbyś model YOLO lub inny
        return None
```

#### 2.4 Przykładowy Kod: Translator Akcji

```python
# middleware/action_translator.py

"""
Tłumaczy abstrakcyjne akcje z Concordia na konkretne komendy dla robota.
"""

import logging
from typing import Dict, List

logger = logging.getLogger(__name__)


class ActionTranslator:
    """
    Translator między językiem naturalnym akcji a komendami robota.
    
    Proces:
    Concordia: "Robot powinien się przywitać" 
    → ActionTranslator: {'type': 'gesture', 'name': 'wave'} + {'type': 'speak', 'text': 'Witam'}
    → G1Interface: Wykonanie gestu machania + odtworzenie audio
    """
    
    def __init__(self):
        """Inicjalizacja translatora z mapowaniem akcji."""
        logger.info("Inicjalizacja ActionTranslator")
        
        # Słownik mapujący intencje na akcje
        self.action_mappings = {
            'greet': self._create_greeting_action,
            'move_forward': self._create_move_forward_action,
            'pick_object': self._create_pick_object_action,
            'answer_question': self._create_answer_action,
            'wave': self._create_wave_action,
        }
    
    def translate_agent_action(self, agent_output: str) -> List[Dict]:
        """
        Tłumaczy output agenta Concordia na listę akcji do wykonania.
        
        Args:
            agent_output: Tekst wygenerowany przez agenta
                         Przykład: "Powinienem przywitać osobę i zapytać czy mogę pomóc"
        
        Returns:
            Lista słowników z akcjami do wykonania
            Przykład: [
                {'type': 'speak', 'text': 'Witam!'},
                {'type': 'gesture', 'name': 'wave'},
                {'type': 'speak', 'text': 'Czy mogę w czymś pomóc?'}
            ]
        """
        logger.info(f"Tłumaczenie akcji agenta: {agent_output}")
        
        actions = []
        
        # Analiza output agenta i ekstrakcja intencji
        # To może być proste parsowanie tekstu lub użycie NLP
        intent = self._extract_intent(agent_output)
        
        # Pobranie odpowiedniego translatora dla intencji
        action_creator = self.action_mappings.get(intent)
        
        if action_creator:
            actions = action_creator(agent_output)
        else:
            # Jeśli nie znamy intencji, robot może tylko powtórzyć co agent powiedział
            logger.warning(f"Nieznana intencja: {intent}")
            actions = [{'type': 'speak', 'text': agent_output}]
        
        return actions
    
    def _extract_intent(self, text: str) -> str:
        """
        Ekstrakcja intencji z tekstu agenta.
        
        Uproszczona wersja - sprawdza słowa kluczowe.
        Zaawansowana wersja mogłaby użyć modelu NLU (Natural Language Understanding).
        """
        text_lower = text.lower()
        
        if any(word in text_lower for word in ['witaj', 'cześć', 'dzień dobry', 'przywitać']):
            return 'greet'
        elif any(word in text_lower for word in ['idź', 'porusz', 'naprzód', 'do przodu']):
            return 'move_forward'
        elif any(word in text_lower for word in ['podnieś', 'weź', 'chwyć', 'złap']):
            return 'pick_object'
        elif any(word in text_lower for word in ['odpowiedz', 'powiedz', 'wyjaśnij']):
            return 'answer_question'
        elif any(word in text_lower for word in ['pomachaj', 'wave']):
            return 'wave'
        
        return 'unknown'
    
    def _create_greeting_action(self, text: str) -> List[Dict]:
        """Tworzy sekwencję akcji powitania."""
        return [
            {'type': 'gesture', 'name': 'wave'},
            {'type': 'speak', 'text': 'Witam! Miło Cię widzieć.'},
            {'type': 'gesture', 'name': 'slight_bow'}
        ]
    
    def _create_move_forward_action(self, text: str) -> List[Dict]:
        """Tworzy akcję ruchu do przodu."""
        # Można wyekstrahować dystans z tekstu
        distance = self._extract_distance(text)
        return [
            {'type': 'speak', 'text': f'Poruszam się do przodu o {distance} metr.'},
            {'type': 'move', 'direction': 'forward', 'distance': distance}
        ]
    
    def _create_pick_object_action(self, text: str) -> List[Dict]:
        """Tworzy sekwencję akcji podnoszenia obiektu."""
        # W pełnej implementacji wykryłbyś pozycję obiektu z kamery
        return [
            {'type': 'speak', 'text': 'Podnoszę obiekt.'},
            {'type': 'manipulate', 'action': 'grasp', 'object_position': [0.5, 0, 0.3]},
            {'type': 'speak', 'text': 'Mam obiekt.'}
        ]
    
    def _create_answer_action(self, text: str) -> List[Dict]:
        """Tworzy akcję odpowiedzi na pytanie."""
        # Wyekstrahuj odpowiedź z tekstu agenta
        answer = self._extract_answer(text)
        return [
            {'type': 'speak', 'text': answer}
        ]
    
    def _create_wave_action(self, text: str) -> List[Dict]:
        """Tworzy akcję machania ręką."""
        return [
            {'type': 'gesture', 'name': 'wave'}
        ]
    
    def _extract_distance(self, text: str) -> float:
        """Ekstrahuje dystans z tekstu."""
        # Uproszczona ekstrakcja - szuka liczb
        import re
        numbers = re.findall(r'\d+\.?\d*', text)
        return float(numbers[0]) if numbers else 1.0  # domyślnie 1 metr
    
    def _extract_answer(self, text: str) -> str:
        """Ekstrahuje odpowiedź z tekstu agenta."""
        # W tym przypadku, cały tekst jest odpowiedzią
        return text
```

### Krok 3: Integracja z Concordia

#### 3.1 Tworzenie Agenta Robota

```python
# concordia_agent/robot_agent.py

"""
Definicja agenta Concordia reprezentującego robota Unitree G1.
"""

from concordia import components
from concordia.prefabs import entity as entity_prefabs
from concordia.typing import entity as entity_lib
import logging

logger = logging.getLogger(__name__)


def create_robot_agent(
    name: str = "RobotG1",
    model: any = None,  # Model językowy (LLM)
    embedder: any = None,  # Embedder dla pamięci
) -> entity_lib.Entity:
    """
    Tworzy agenta reprezentującego robota Unitree G1.
    
    Args:
        name: Nazwa robota
        model: Model językowy do generowania decyzji
        embedder: Model do tworzenia embeddingów dla pamięci
    
    Returns:
        Skonfigurowany agent robota
        
    Architektura agenta:
    - Pamięć: Co robot pamięta (poprzednie interakcje, zadania)
    - Obserwacja: Co robot widzi i czuje (dane z czujników)
    - Planowanie: Jak robot decyduje co zrobić
    - Działanie: Co robot faktycznie robi
    """
    logger.info(f"Tworzenie agenta robota: {name}")
    
    # Opis robota - to kształtuje jego "osobowość"
    robot_description = """
    Jestem robotem humanoidalnym Unitree G1 pracującym w laboratorium 
    Politechniki Rzeszowskiej. Moim celem jest asystowanie studentom 
    i pracownikom uczelni.
    
    Moje cechy:
    - Przyjazny i pomocny
    - Bezpieczny (zawsze dbam o bezpieczeństwo ludzi)
    - Komunikatywny (lubię rozmawiać z ludźmi)
    - Staranny (wykonuję zadania precyzyjnie)
    
    Moje możliwości:
    - Poruszanie się po pomieszczeniach
    - Manipulowanie obiektami
    - Prowadzenie konwersacji
    - Rozpoznawanie osób i obiektów
    """
    
    # Tworzenie komponentów agenta
    
    # 1. PAMIĘĆ - Co robot pamięta
    memory = components.memory.AssociativeMemory(
        model=model,
        embedder=embedder,
        clock=None,  # Będzie dodany później
        add_time=True
    )
    
    # 2. OBSERWACJA - Co robot postrzega
    # Ten komponent będzie otrzymywać przetworzone dane z czujników
    observation = components.observation.Observation(
        agent_name=name,
        clock=None,  # Będzie dodany później
        memory=memory,
    )
    
    # 3. PLANOWANIE - Jak robot myśli
    # Inspirowane March & Olsen (2011) - trzy pytania:
    # - Jaka jest sytuacja?
    # - Kim jestem?
    # - Co powinienem zrobić?
    
    situation_perception = components.agent.SituationPerception(
        model=model,
        memory=memory,
        agent_name=name,
        components=[]
    )
    
    self_perception = components.agent.SelfPerception(
        model=model,
        agent_name=name,
        description=robot_description
    )
    
    plan = components.agent.Plan(
        model=model,
        observation=observation,
        memory=memory,
    )
    
    # 4. DZIAŁANIE - Co robot robi
    # Ten komponent generuje akcje które później są tłumaczone na komendy robota
    action = components.agent.ActComponent(
        model=model,
        memory=memory,
    )
    
    # 5. KOMPONENTY DODATKOWE specyficzne dla robota
    
    # Monitor bezpieczeństwa - ciągle sprawdza czy robot jest bezpieczny
    safety_monitor = create_safety_component(model, memory)
    
    # Monitor baterii - śledzi poziom energii
    battery_monitor = create_battery_component(model, memory)
    
    # Złożenie wszystkich komponentów w agenta
    agent = entity_prefabs.build_agent(
        name=name,
        act_component=action,
        observation=observation,
        memory=memory,
        components=[
            situation_perception,
            self_perception,
            plan,
            safety_monitor,
            battery_monitor,
        ]
    )
    
    logger.info(f"Agent robota {name} utworzony pomyślnie")
    
    return agent


def create_safety_component(model, memory):
    """
    Komponent monitorujący bezpieczeństwo robota.
    
    Zadania:
    - Sprawdzanie czy robot stoi stabilnie
    - Wykrywanie ludzi w strefie roboczej
    - Blokowanie niebezpiecznych akcji
    """
    # Implementacja komponentu bezpieczeństwa
    # To byłby niestandardowy komponent
    pass


def create_battery_component(model, memory):
    """
    Komponent monitorujący poziom baterii.
    
    Zadania:
    - Śledzenie poziomu naładowania
    - Ostrzeganie gdy bateria jest niska
    - Inicjowanie powrotu do stacji ładowania
    """
    # Implementacja komponentu baterii
    pass
```

### Krok 4: Główny Program Integracyjny

```python
# main.py

"""
Główny program łączący Concordia z robotem Unitree G1.
"""

import asyncio
import logging
from typing import Dict

# Importy z Concordia
from concordia.contrib import language_models
from concordia.prefabs.simulation import generic as simulation

# Importy z middleware
from middleware.g1_interface import UnitreeG1Interface
from middleware.sensor_processor import SensorProcessor
from middleware.action_translator import ActionTranslator

# Importy agenta robota
from concordia_agent.robot_agent import create_robot_agent

# Konfiguracja loggera
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)


class RobotConcordiaIntegration:
    """
    Główna klasa integrująca Concordia z robotem Unitree G1.
    
    Przepływ działania:
    1. Odczyt czujników robota
    2. Przetworzenie na obserwacje dla agenta
    3. Agent podejmuje decyzję (używając Concordia)
    4. Translacja decyzji na komendy robota
    5. Wykonanie komend na robocie
    6. Powrót do kroku 1
    """
    
    def __init__(self, config: Dict):
        """
        Inicjalizacja systemu integracyjnego.
        
        Args:
            config: Słownik konfiguracyjny zawierający:
                - robot_ip: Adres IP robota
                - llm_api_key: Klucz API do modelu językowego
                - llm_model: Nazwa modelu językowego
        """
        logger.info("Inicjalizacja RobotConcordiaIntegration")
        
        self.config = config
        
        # 1. Inicjalizacja interfejsu z robotem
        self.robot = UnitreeG1Interface(config['robot_ip'])
        
        # 2. Inicjalizacja procesora czujników
        self.sensor_processor = SensorProcessor()
        
        # 3. Inicjalizacja translatora akcji
        self.action_translator = ActionTranslator()
        
        # 4. Inicjalizacja modelu językowego dla Concordia
        self.model, self.embedder = self._init_language_model()
        
        # 5. Utworzenie agenta robota
        self.agent = create_robot_agent(
            name="RobotG1_PRz",  # PRz = Politechnika Rzeszowska
            model=self.model,
            embedder=self.embedder
        )
        
        # Stan systemu
        self.is_running = False
        self.loop_delay = config.get('loop_delay', 0.5)  # sekund między cyklami
        
        logger.info("Inicjalizacja zakończona pomyślnie")
    
    def _init_language_model(self):
        """
        Inicjalizacja modelu językowego i embedddera.
        
        Concordia wymaga:
        - Model językowy (LLM) do generowania decyzji
        - Embedder do tworzenia reprezentacji wektorowych dla pamięci
        """
        logger.info("Inicjalizacja modelu językowego")
        
        # Konfiguracja modelu językowego
        model = language_models.language_model_setup(
            api_key=self.config['llm_api_key'],
            model_name=self.config['llm_model'],
            api_type=self.config.get('api_type', 'openai')
        )
        
        # Konfiguracja embedddera (np. sentence-transformers)
        import sentence_transformers
        embedder = sentence_transformers.SentenceTransformer(
            'all-MiniLM-L6-v2'
        )
        
        return model, embedder
    
    async def run(self):
        """
        Główna pętla integracyjna.
        
        To jest "serce" systemu - ciągle:
        1. Czyta czujniki
        2. Przetwarza na obserwacje
        3. Agent myśli
        4. Wykonuje akcje
        5. Powtarza
        """
        logger.info("Uruchamianie głównej pętli integracyjnej")
        self.is_running = True
        
        try:
            while self.is_running:
                # ===== KROK 1: PERCEPCJA =====
                # Odczyt danych z czujników robota
                sensor_data = self.robot.get_sensor_data()
                
                # Przetworzenie na obserwacje zrozumiałe dla agenta
                observation_text = self.sensor_processor.process_all_sensors(
                    sensor_data
                )
                
                logger.info(f"Obserwacja: {observation_text}")
                
                # ===== KROK 2: DELIBERACJA =====
                # Agent analizuje sytuację i podejmuje decyzję
                agent_action = self.agent.act(observation_text)
                
                logger.info(f"Decyzja agenta: {agent_action}")
                
                # ===== KROK 3: TRANSLACJA =====
                # Tłumaczenie decyzji agenta na konkretne akcje robota
                robot_actions = self.action_translator.translate_agent_action(
                    agent_action
                )
                
                logger.info(f"Akcje robota: {robot_actions}")
                
                # ===== KROK 4: WYKONANIE =====
                # Wykonanie akcji na robocie
                for action in robot_actions:
                    success = self.robot.execute_action(action)
                    if not success:
                        logger.warning(f"Akcja nie powiodła się: {action}")
                
                # Opóźnienie przed następną iteracją
                await asyncio.sleep(self.loop_delay)
                
        except KeyboardInterrupt:
            logger.info("Przerwanie przez użytkownika")
        except Exception as e:
            logger.error(f"Błąd w głównej pętli: {e}", exc_info=True)
        finally:
            self.shutdown()
    
    def shutdown(self):
        """Bezpieczne wyłączenie systemu."""
        logger.info("Wyłączanie systemu")
        self.is_running = False
        
        # Zatrzymanie robota
        self.robot.emergency_stop()
        
        # Rozłączenie
        self.robot.disconnect()
        
        logger.info("System wyłączony")


# ===== PUNKT WEJŚCIA PROGRAMU =====

if __name__ == "__main__":
    # Konfiguracja
    config = {
        'robot_ip': '192.168.123.10',  # Adres IP robota Unitree G1
        'llm_api_key': 'YOUR_API_KEY_HERE',  # Twój klucz API
        'llm_model': 'gpt-4',  # Model językowy
        'api_type': 'openai',
        'loop_delay': 0.5  # Opóźnienie między cyklami (sekundy)
    }
    
    # Utworzenie systemu integracyjnego
    integration = RobotConcordiaIntegration(config)
    
    # Uruchomienie
    asyncio.run(integration.run())
```

## Testowanie Integracji

### Test 1: Symulacja Bez Fizycznego Robota

Przed testami na prawdziwym robocie, przetestuj system z "wirtualnym" robotem:

```python
# tests/test_integration.py

"""Testy integracji bez fizycznego robota."""

class MockRobotInterface:
    """Symulowany robot do testów."""
    
    def get_sensor_data(self):
        """Zwraca przykładowe dane z czujników."""
        import numpy as np
        return {
            'camera_frame': np.random.rand(480, 640, 3),
            'imu_data': {
                'orientation': [1, 0, 0, 0],
                'angular_velocity': [0, 0, 0],
                'linear_acceleration': [0, 0, 9.81]
            },
            'battery_level': 85
        }
    
    def execute_action(self, action):
        """Symuluje wykonanie akcji."""
        print(f"[MOCK ROBOT] Wykonuję: {action}")
        return True


# Test
if __name__ == "__main__":
    mock_robot = MockRobotInterface()
    
    # Test odczytu czujników
    data = mock_robot.get_sensor_data()
    print(f"Dane z czujników: {data}")
    
    # Test wykonania akcji
    mock_robot.execute_action({'type': 'speak', 'text': 'Test'})
```

### Test 2: Test na Prawdziwym Robocie (Stopniowo)

1. **Faza 1**: Test tylko odczytu czujników
2. **Faza 2**: Test prostych akcji (mówienie)
3. **Faza 3**: Test ruchów (poruszanie się)
4. **Faza 4**: Test złożonych zachowań

## Bezpieczeństwo

### Zasady Bezpieczeństwa

1. **Zawsze miej przycisk STOP** - Fizyczny przycisk awaryjnego zatrzymania
2. **Testuj w bezpiecznej przestrzeni** - Obszar bez ludzi i cennych przedmiotów
3. **Monitoruj ciągle** - Ktoś zawsze obserwuje robota podczas testów
4. **Ograniczenia prędkości** - Na początku testów ogranicz maksymalną prędkość
5. **Tryb symulacji** - Zawsze najpierw testuj w symulacji

### Implementacja Monitor Bezpieczeństwa

```python
# middleware/safety_monitor.py

class SafetyMonitor:
    """Monitor bezpieczeństwa robota."""
    
    def check_safety(self, sensor_data: Dict, planned_action: Dict) -> bool:
        """
        Sprawdza czy planowana akcja jest bezpieczna.
        
        Returns:
            True jeśli akcja jest bezpieczna, False jeśli należy ją zablokować
        """
        # Sprawdź poziom baterii
        if sensor_data['battery_level'] < 10:
            logger.critical("CRITICAL: Bardzo niski poziom baterii!")
            return False
        
        # Sprawdź stabilność
        imu_obs = process_imu(sensor_data['imu_data'])
        if not imu_obs['is_stable']:
            logger.warning("WARNING: Robot niestabilny, blokowanie ruchu")
            return False
        
        # Sprawdź czy są ludzie w strefie roboczej
        camera_obs = process_camera(sensor_data['camera_frame'])
        if planned_action['type'] == 'move':
            for person in camera_obs['people_detected']:
                if person['distance'] < 1.0:  # 1 metr
                    logger.warning("WARNING: Człowiek zbyt blisko, blokowanie ruchu")
                    return False
        
        return True
```

## Troubleshooting

### Problem 1: Robot się nie łączy
**Rozwiązanie:**
- Sprawdź IP robota (ping)
- Sprawdź czy robot jest włączony
- Sprawdź czy jesteś w tej samej sieci

### Problem 2: Agent Concordia nie reaguje logicznie
**Rozwiązanie:**
- Sprawdź promptы - czy opis agenta jest jasny
- Sprawdź obserwacje - czy agent dostaje sensowne informacje
- Dodaj więcej przykładów do pamięci agenta

### Problem 3: Robot wykonuje niebezpieczne ruchy
**Rozwiązanie:**
- NATYCHMIAST użyj przycisku STOP
- Przejrzyj logi - co agent "myślał"
- Wzmocnij safety monitor
- Ogranicz możliwe akcje

## Dalsze Kroki

1. **Optymalizacja**: Zredukuj opóźnienia w pętli
2. **Uczenie się**: Dodaj mechanizm uczenia się z doświadczenia
3. **Multimodalność**: Dodaj więcej typów czujników
4. **Społeczność**: Połącz z innymi robotami (multi-agent)

## Zakończenie

Ta integracja to dopiero początek! Możesz rozwijać system w wielu kierunkach:
- Bardziej zaawansowane rozumienie języka
- Lepsze planowanie długoterminowe
- Uczenie przez demonstrację
- Współpraca między wieloma robotami

Powodzenia w projekcie! 🤖🚀
