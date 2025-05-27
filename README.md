# RotaTabanl-sagl-kotomasyonu
Rota Tabanlı sağlık otomasyonu -Veri Yapıları Dersi- JavaFx ile yapıldı.

🗺️ Graph Sınıfı
javapublic class Graph {
    // Ana veri yapısı - Adjacency List
    private Map<Node, List<Edge>> adjacencyList;
    private int nodeCount;
    private int edgeCount;
}
🔍 Veri Yapısı Analizi:

Map<Node, List<Edge>>: HashMap + ArrayList kombinasyonu
HashMap Performans: O(1) node erişimi
ArrayList Performans: O(1) edge erişimi (index ile)
Toplam Arama: O(1) + O(1) = O(1) komşu bulma

📈 Neden Bu Yapı?

Adjacency Matrix: O(V²) alan - 12x12=144 hücre gerekir
Adjacency List: O(V+E) alan - Sadece var olan bağlantılar
İstanbul Haritası: Sparse graph (seyrek graf) için ideal


🏥 Node Sınıfı
javapublic class Node {
    private String id;                    // String - Unique identifier
    private String displayName;           // String - UI görünen isim
    private Point2D coordinates;          // Object - X,Y koordinatları  
    private NodeType type;               // Enum - EV, HASTANE
}
🔍 Veri Yapısı Analizi:

Primitive String: Immutable, memory efficient
Point2D Object: Double precision coordinates
Enum NodeType: Memory efficient, type safety
Toplam boyut: ~64 bytes per node

📊 Karşılaştırma:
Alternatif 1: int[] coordinates     → Less precision
Alternatif 2: String coordinates    → Parsing overhead  
Seçimimiz: Point2D                  → Clean, precise ✅

🛣️ Edge Sınıfı
javapublic class Edge {
    private Node destination;             // Object reference
    private double weight;               // Primitive double
    private double congestion;           // Primitive double  
    private String roadType;             // String
    private boolean isBlocked;           // Primitive boolean
}
🔍 Veri Yapısı Analizi:

Node Reference: 8 bytes (64-bit JVM)
double weight/congestion: 8 bytes each
String roadType: Variable length
boolean isBlocked: 1 byte
Toplam: ~40-50 bytes per edge

⚡ Performance Insights:

Direct Node reference: O(1) destination access
Primitive doubles: Fast arithmetic operations
String interning: "Ana Cadde" string reuse


🎮 HelloController Sınıfı
javapublic class HelloController {
    // Hash Maps - O(1) erişim
    private Map<Node, Point2D> nodeCoordinates;           // HashMap
    private Map<Node, PatientRecord> patientRecords;      // HashMap  
    private Map<String, Double> ambulanceTimers;         // HashMap
    private Map<String, List<Point2D>> ambulanceRoutes;  // HashMap + ArrayList
    
    // Dynamic Arrays - O(1) erişim, dinamik boyut
    private List<Node> homeNodes;                         // ArrayList
    private List<Hospital> hospitals;                     // ArrayList
    
    // UI Binding Lists - Observable pattern
    private ObservableList<String> activeAmbulances;     // JavaFX ObservableList
    private ObservableList<String> completedMissions;    // JavaFX ObservableList
    
    // Sets - O(1) arama, unique values
    private Set<String> allSpecialties;                  // TreeSet (sorted)
}
🔍 Veri Yapısı Detayları:
HashMap Kullanımları:
java// Coordinate mapping - O(1) coordinate lookup
nodeCoordinates.put(homeNode1, new Point2D(300, 150));

// Patient records - O(1) patient search  
patientRecords.put(selectedNode, new PatientRecord(...));

// Timer tracking - O(1) ambulance time lookup
ambulanceTimers.put("AMB001", 125.5);
ArrayList Kullanımları:
java// Home locations - O(1) index access
List<Node> homeNodes = Arrays.asList(home1, home2, ..., home12);

// Hospital list - O(1) index access + dynamic resizing
hospitals.add(new Hospital("Acıbadem", specialties));
ObservableList (JavaFX):
java// UI otomatik güncelleme - Observer pattern
activeAmbulances.add("AMB001 - Görevde"); // UI immediately updates
completedMissions.add("Görev #125 Tamamlandı"); // ListView refreshes

🏥 Hospital Sınıfı
javapublic class Hospital {
    private String name;                          // String
    private int ambulanceCount;                   // Primitive int
    private List<String> specialties;            // ArrayList<String>
    private Queue<EmergencyCall> emergencyQueue; // LinkedList implementation
    private Set<MedicalStaff> availableStaff;   // HashSet
}
🔍 Veri Yapısı Analizi:
ArrayList<String> specialties:
java// Dynamic specialty list
specialties = Arrays.asList("Acil Tıp", "Kalp Cerrahisi", "Nöroloji");
// O(1) access, O(N) search, dynamic sizing
Queue<EmergencyCall> emergencyQueue:
java// FIFO implementation with LinkedList
Queue<EmergencyCall> queue = new LinkedList<>();
queue.offer(emergencyCall);    // O(1) enqueue
EmergencyCall next = queue.poll(); // O(1) dequeue
HashSet<MedicalStaff>:
java// Unique staff members, O(1) lookup
availableStaff.add(new MedicalStaff("Dr. Mehmet", "Acil Tıp"));
boolean isAvailable = availableStaff.contains(staff); // O(1)

👤 PatientRecord Sınıfı
javapublic class PatientRecord {
    private String tcNo;                                // String - Primary key
    private String fullName;                           // String
    private int age;                                   // Primitive int
    private BloodType bloodType;                       // Enum
    private List<MedicalHistory> medicalHistory;       // ArrayList
    private List<EmergencyCall> emergencyCalls;        // ArrayList
    private Set<String> allergies;                     // HashSet - Unique allergies
    private Set<String> medications;                   // HashSet - Current meds
    private Priority currentPriority;                  // Enum
}
🔍 Veri Yapısı Detayları:
ArrayList<MedicalHistory>:
java// Chronological medical history
medicalHistory.add(new MedicalHistory("2023-01-15", "Kalp Kontrolü"));
// O(1) append, O(N) search, maintains order
HashSet<String> allergies:
java// Unique allergy list - no duplicates
allergies.add("Penisilin");
allergies.add("Penisilin"); // Ignored - already exists
boolean hasAllergy = allergies.contains("Penisilin"); // O(1)
Enum Priority:
javaenum Priority {
    CRITICAL(4), HIGH(3), MEDIUM(2), LOW(1);
    private final int level;
}
// Memory efficient, type safe, comparable

🚑 AmbulanceSystem Sınıfı
javapublic class AmbulanceSystem {
    // Mission tracking - O(1) lookup
    private Map<String, AmbulanceMission> activeMissions;    // HashMap
    
    // Priority management - O(log N) operations  
    private PriorityQueue<EmergencyCall> priorityQueue;      // Min-Heap
    
    // Standard queue - O(1) operations
    private Queue<EmergencyCall> emergencyQueue;             // LinkedList
    
    // Hospital references - O(1) access
    private List<Hospital> hospitals;                        // ArrayList
    
    // Graph reference - Composition relationship  
    private Graph cityGraph;                                 // Object reference
}
🔍 Veri Yapısı Detayları:
HashMap<String, AmbulanceMission>:
java// Mission tracking with ambulance ID as key
activeMissions.put("AMB001", new AmbulanceMission(...));
AmbulanceMission mission = activeMissions.get("AMB001"); // O(1) lookup
PriorityQueue<EmergencyCall>:
java// Min-heap implementation - highest priority first
PriorityQueue<EmergencyCall> pq = new PriorityQueue<>(
    Comparator.comparing(EmergencyCall::getPriority).reversed()
);
pq.offer(emergencyCall);           // O(log N) insertion
EmergencyCall urgent = pq.poll();  // O(log N) extraction
Graph Reference:
java// Uses composition - AmbulanceSystem HAS-A Graph
private Graph cityGraph;
// Delegation pattern for shortest path calculations

⚕️ MedicalStaffSystem Sınıfı
javapublic class MedicalStaffSystem {
    // Nested data structure - HashMap of ArrayLists
    private Map<String, List<MedicalStaff>> hospitalStaff;   // HashMap + ArrayList
    
    // Available staff queue - FIFO
    private Queue<MedicalStaff> availableStaff;             // LinkedList
    
    // On-duty tracking - O(1) lookup
    private Set<String> onDutyStaff;                        // HashSet
    
    // Shift schedules - Time-based mapping
    private Map<LocalDateTime, List<String>> shiftSchedule;  // HashMap + ArrayList
}
🔍 Nested Veri Yapısı:
java// Hospital → Staff mapping
Map<String, List<MedicalStaff>> hospitalStaff = new HashMap<>();
hospitalStaff.put("Acıbadem", Arrays.asList(staff1, staff2, staff3));

// O(1) hospital lookup + O(N) staff iteration
List<MedicalStaff> acibademStaff = hospitalStaff.get("Acıbadem");

🌤️ WeatherTrafficSystem Sınıfı
javapublic class WeatherTrafficSystem {
    // Real-time traffic data - O(1) lookup
    private Map<String, Double> trafficData;                // HashMap
    
    // Weather conditions - Enum for efficiency
    private Map<String, WeatherCondition> weatherData;      // HashMap + Enum
    
    // Time-series data - Ordered by time
    private NavigableMap<LocalDateTime, TrafficSnapshot> historicalData; // TreeMap
}
🔍 Time-Series Veri Yapısı:
java// TreeMap for ordered time data - O(log N) operations
NavigableMap<LocalDateTime, TrafficSnapshot> data = new TreeMap<>();
data.put(LocalDateTime.now(), new TrafficSnapshot(2.5)); // Traffic multiplier

// Range queries - get traffic between times
Map<LocalDateTime, TrafficSnapshot> range = 
    data.subMap(startTime, endTime); // O(log N + K)

📊 Veri Yapısı Karmaşıklık Özeti
🏆 En Sık Kullanılan Yapılar:
Veri YapısıSınıfAramaEklemeKullanım AmacıHashMapHelloController, AmbulanceSystemO(1)O(1)Hızlı aramaArrayListHospital, PatientRecordO(1)O(1)*Dinamik listelemeHashSetPatientRecord, MedicalStaffO(1)O(1)Benzersiz veriPriorityQueueAmbulanceSystemO(log N)O(log N)Öncelik yönetimiLinkedListHospital (Queue)O(N)O(1)FIFO operasyonları
🎯 Kritik Performans Noktaları:

Dijkstra Algoritması: O(V log V + E) - Graph sınıfında
Hasta Arama: O(1) - HashMap kullanımı
Öncelik Sıralaması: O(log N) - PriorityQueue
UI Güncellemeleri: O(1) - ObservableList binding
