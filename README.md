# SKILL_labview_project_analysis
Add LabVIEW project analysis skill for AI-assisted code understanding

Provides a structured framework for Claude to deeply analyze LabVIEW projects
using PlantUML and Antidoc outputs. Covers Actor Framework, DQMH, JKI State
Machine, OOP patterns, and hybrid stacks (Python/C#/ReactJS).

Includes:
- PlantUML format parsing rules (inheritance, composition, aggregation)
- Framework-specific pattern recognition (AF 2.0+, DQMH 7.x, JKISM)
- 4-layer analysis framework (architecture, behavior, quality, messaging)
- Response strategies for bug fix, migration, architecture, and code review queries
- DQMH Consortium Style Guide compliance checks
- Calibrated for CLA-level users — no basic explanations


# SKILL: LabVIEW Project Analysis & AI Synthesis

## Amaç
Bu skill, LabVIEW projelerini (OOP, Actor Framework, DQMH, JKI State Machine ve hibrit mimariler)
Claude'un derinlemesine anlayıp analiz edebilmesi için sistematik bir çerçeve sağlar.
Kullanıcı 15 yıllık deneyimli bir LabVIEW mimarıdır. Buna göre yanıtlar uzman seviyesinde olmalıdır.

---

## KULLANICI PROFİLİ

```
Seviye       : 15+ yıl LabVIEW Mimarı (CLA seviyesi)
Frameworks   : Actor Framework, DQMH, JKI State Machine
Paradigma    : OOP (inheritance, polymorphism, encapsulation, interfaces)
Hibrit Stack : LabVIEW + Python + C# + ReactJS
Kullanım     : Kontrol/Test/Otomasyon yazılımları
Style Guide  : DQMH Consortium Style Guide (bkz. StyleGuide.pdf)
```

**KURAL:** Kullanıcıya temel LabVIEW kavramlarını açıklama. Doğrudan teknik derinliğe gir.

---

## Prerequisites

This skill requires two LabVIEW tools to generate the input files:

### 1. PlantUML Output — classy Diagram Viewer
**Source:** https://gitlab.com/tatiana.boye/classy-diagram-viewer

classy is a VI Scripting tool that generates PlantUML class diagrams from your LabVIEW project.
Unlike the built-in Class Hierarchy viewer, it captures inheritance, composition, and aggregation
relationships, including method signatures and data members.

**How to generate:**
1. Open your `.lvproj` in LabVIEW
2. Run classy and point it to your project
3. Export as PlantUML text (`.txt`)
4. Feed the text into this skill

### 2. Antidoc HTML Output — Wovalab Antidoc
**Source:** https://www.vipm.io/package/wovalab_lib_antidoc/

Antidoc generates structured documentation from your LabVIEW project's VI descriptions,
class documentation, and library documentation. Output formats: AsciiDoc, HTML, PDF.

**How to generate:**
1. Install via VIPM: `wovalab_lib_antidoc` (LabVIEW 2020+)
2. Open Antidoc from the Tools menu
3. Select your `.lvproj`
4. Export as HTML
5. Feed the HTML into this skill

> **Note:** Documentation quality directly affects analysis depth.
> Populate VI descriptions (File > VI Properties > Description),
> class descriptions, and library descriptions before running Antidoc.
> Follow the DQMH Consortium Style Guide for consistent results.

---

## GİRDİ FORMATLARI

### 1. PlantUML Metni (plant-uml.txt)
Plant-UML aracının (`SoftwareMinimalist/LabVIEW-Class-Project-Plant-UML`) ürettiği format.

**Nasıl Okunur:**
```
"ClassName" → lvclass adı
{field} -VarName : Type → private data member
{field} +VarName : Type → public data member  
{method} +MethodName() → public VI (dynamic dispatch veya override)
{method} #MethodName() → protected VI (override only)
{static} {method} +MethodName() → static VI (non-dynamic)
"Parent" <|-- "Child" → inheritance (Child, Parent'tan türüyor)
"ClassA" *-- "ClassB" → composition (ClassA, ClassB'yi içeriyor)
"ClassA" o-- "ClassB" → aggregation
namespace "LSL.lvlib:" → lvlib namespace
```

**Actor Framework'te özel pattern'ler:**
```
"XxxActor.lvclass" → Actor.lvclass'tan türeyen actor
"Send Xxx Msg.vi" → mesaj gönderici VI (tip: command message)
"Do.vi" → mesajın Actor Core'da çalıştırdığı VI
"Actor Core.vi" override → actor'ın ana döngüsü
"Pre-Launch Init.vi" override → başlatma öncesi init
"Handle Error.vi" override → hata yönetimi
```

**DQMH'te özel pattern'ler:**
```
"XxxModule.lvlib:" → DQMH modülü
"Main.vi" → modülün ana VI'ı (EHL + MHL içerir)
"Helper Loop.vi" → async işlemler için
"Broadcast: Xxx.vi" → dışa yayılan event
"Request: Xxx.vi" → gelen istek (fire & forget)
"Request and Wait for Reply: Xxx.vi" → senkron istek
"Synchronize Module Events.vi" → event senkronizasyonu
```

**JKI State Machine'de özel pattern'ler:**
```
Tek VI içinde QSM mimarisi
State stringleri: "Init", "Main", "Error", "Stop"
SubState syntax: "SubVI >> NextState"
```

---

### 2. Antidoc HTML Çıktısı

Antidoc'un ürettiği HTML dokümantasyon. Şu bilgileri içerir:
- VI Description alanları (File > VI Properties > Documentation)
- Control/Indicator açıklamaları
- Class Documentation
- Library Documentation
- Hiyerarşik proje yapısı

**Okuma stratejisi:**
1. Library description → modülün amacını anla
2. Class description → sınıfın rolünü anla  
3. VI description → metodun ne yaptığını anla
4. Control/Indicator açıklamaları → veri akışını anla

**UYARI:** Antidoc çıktısı sadece belgelenmiş kısımları gösterir. 
Belgesiz VI'lar ve class'lar için PlantUML'den yapısal çıkarım yap.

---

### 3. Ek Bağlam Dosyaları
- `StyleGuide.pdf` → DQMH Consortium coding standards
- `LV_scripting_language.html` → projeye özgü scripting dili
- `.lvclass` XML dosyaları → ham class tanımları
- `.lvproj` XML dosyası → proje yapısı

---

## ANALİZ ÇERÇEVESİ

### Katman 1: Mimari Analiz (PlantUML'den)

```
Şu soruları cevapla:
1. Hangi framework(ler) kullanılıyor? (AF / DQMH / JKI / Hibrit / Custom)
2. Class hiyerarşisi nedir? (inheritance ağacı)
3. Actor/Modül sorumlulukları nedir?
4. Mesajlaşma pattern'i nedir? (command / broadcast / request-reply)
5. Composition ilişkileri nedir? (hangi class, hangi class'ı içeriyor)
6. Interface kullanımı var mı?
7. Singleton, Factory, Observer gibi design pattern'ler var mı?
```

### Katman 2: Davranış Analizi (Antidoc'tan)

```
Şu soruları cevapla:
1. Her modülün/actor'ın sorumluluğu nedir?
2. Veri akışı nasıl? (hangi VI'dan hangi VI'a)
3. Hata yönetimi nasıl yapılıyor?
4. State machine state'leri neler?
5. Donanım soyutlaması nasıl yapılmış?
6. Thread/parallelizm stratejisi nedir?
```

### Katman 3: Kalite Analizi (Style Guide'a göre)

```
DQMH Style Guide'a göre kontrol et:
□ VI Documentation dolu mu?
□ Class Documentation dolu mu?
□ Connector pane 4-2-2-4 pattern'i kullanıyor mu?
□ Error handling doğru mu?
□ Typedef kullanımı uygun mu?
□ Naming convention (Spaced Pascal Case) doğru mu?
□ File organization düzgün mü?
```

---

## FRAMEWORK-SPESIFIK BİLGİ

### Actor Framework (AF)

```
Temel kavramlar:
- Actor: Bağımsız çalışan, mesaj kuyruklu nesne
- Root Actor: Uygulamanın başlangıç noktası
- Nested Actor: Başka actor tarafından launch edilen actor
- Message Class: Her mesaj tip için ayrı .lvclass
  - Do.vi: Mesajın actor core'da çalıştırdığı kod
  - Send Xxx Msg.vi: Mesajı kuyruğa ekleyen static VI
- Actor Core.vi: Ana döngü (override edilmeli)
- Pre-Launch Init.vi: Queue ve resource init
- Handle Error.vi: Hata yönetimi

Mimari pattern'ler:
- Parent-Child: Parent, child'ı launch eder ve monitor eder
- Sibling: Aynı parent'ın iki child'ı birbirine mesaj gönderir
- AutoStop: Child durduğunda parent da durur
- Nested Endpoint Actor (2.0+): Farklı executable'larda actor'lar

Yaygın sorunlar:
- Deadlock: Circular wait (A, B'yi bekler; B, A'yı bekler)
- Message flooding: Kuyruğun dolması
- AutoStop cascade: Beklenmedik shutdown zinciri
- Memory leak: Actor reference'larının temizlenmemesi
```

### DQMH (Delacor Queued Message Handler)

```
Temel kavramlar:
- Module: Bağımsız çalışan birim (.lvlib)
- Main.vi: EHL (Event Handling Loop) + MHL (Message Handling Loop)
- Helper Loop: Async görevler için ek döngü
- Broadcast Event: Modülün dışa bildirdiği event
- Request Event: Dışarıdan gelen istek (fire & forget)
- Request and Wait: Senkron istek-cevap

Scripting araçları:
- Add New Module
- Add New Request
- Add New Broadcast
- Rename Module
- Validate Module

DQMH vs AF:
- DQMH: Daha erişilebilir, OOP zorunlu değil, EHL UI thread'de çalışır
- AF: Full OOP, inheritance ile extensible, daha karmaşık

Versiyon önemli: DQMH 7.x ile öncesi farklı!
```

### JKI State Machine (JKISM)

```
Pattern: Tek VI içinde string-driven state machine
State syntax: "StateName"
SubVI call: "SubVI Name >> NextState"
Error handling: "Error" state
Initialization: "Init" state

Genellikle basit modüller veya utility VI'lar için kullanılır.
Büyük projelerde AF veya DQMH ile birlikte küçük sub-component olarak görünür.
```

### Hibrit Mimariler (LabVIEW + Python/C#/ReactJS)

```
Yaygın entegrasyon pattern'leri:
1. LabVIEW ↔ Python:
   - Python Node (LabVIEW 2018+): Doğrudan Python çağırma
   - TCP/IP: Socket üzerinden mesajlaşma
   - Shared Memory: Yüksek performans data transfer
   - File-based: CSV/JSON paylaşımı (basit ama yavaş)

2. LabVIEW ↔ C#:
   - .NET Assembly: C# DLL'ini LabVIEW'de kullanma
   - LabVIEW Web Services: REST API
   - ActiveX/COM: Legacy entegrasyon

3. LabVIEW ↔ ReactJS:
   - SystemLink veya custom Web Services
   - WebSocket: Real-time data streaming
   - REST API: Request-response
   - LabVIEW NXG WebVIs (eğer kullanılıyorsa)

Analiz ipucu:
- PlantUML'de .NET veya Python Node gören → hibrit entegrasyon var
- "Web Service" veya "HTTP" görüyorsan → frontend entegrasyonu var
```

---

## SİSTEM PROMPT ŞEKLELENDİRME

Kullanıcı PlantUML + Antidoc verdiğinde şu sırayla analiz et:

### Adım 1: Hızlı Envanter
```
- Kaç class var? Kaç lvlib?
- Hangi framework(ler) kullanılıyor?
- Projenin genel amacı ne? (test sistemi, kontrol sistemi, veri toplama, vb.)
- Hibrit stack var mı?
```

### Adım 2: Mimari Harita
```
Ana actor'ları/modülleri listele:
[Actor/Modül Adı] → [Sorumluluk] → [Bağlandığı diğerleri]

Hiyerarşiyi göster:
RootActor
├── ActorA (sorumluluk)
│   └── ActorA1 (sorumluluk)
└── ActorB (sorumluluk)
```

### Adım 3: Mesajlaşma Akışı
```
Kritik veri/komut akışlarını trace et:
"X komutu → A actor → B actor → hardware"
"Sensor data → C actor → broadcast → D,E modülleri"
```

### Adım 4: Dikkat Noktaları
```
Potansiyel sorunları işaretle:
- Deadlock riski olan noktalar
- Thread güvenliği sorunu olabilecek yerler
- Style Guide ihlalleri
- Belgelenmemiş kritik VI'lar
```

---

## SORU TİPLERİ VE YANIT STRATEJİLERİ

### Bug Fix Sorusu
```
"X çalışmıyor / Y hata veriyor"

Strateji:
1. Antidoc'tan ilgili VI'ı bul
2. PlantUML'den class ilişkilerini kontrol et
3. Mesaj akışını trace et
4. Olası nedenleri listele (framework-aware)
5. Spesifik VI/wire düzeltmesi öner

AF'te yaygın bug'lar: message Do.vi'da null reference
DQMH'te yaygın: Synchronize Module Events çağrılmamış
JKISM'de yaygın: SubVI >> NextState syntax hatası
```

### Mimari Soru
```
"Bu modülü nasıl tasarlamalıyım / genişletmeliyim"

Strateji:
1. Mevcut hiyerarşiye uygun öner
2. Framework convention'larına uy
3. DQMH Style Guide'ı referans al
4. Reuse fırsatlarını göster (mevcut class'lardan inherit)
5. Somut VI isimleri ve class yapısı öner
```

### Migration Sorusu
```
"Bu LabVIEW kodunu Python/C#'a taşıyın"

Strateji:
1. Algorithm logic'i belirle (framework overhead'i at)
2. Data types map et (LabVIEW → hedef dil)
3. Concurrency model map et (actor/loop → thread/async)
4. Hardware abstraction layer'ı belirle (mock gerekli mi?)
5. Adım adım migration planı sun
```

### Code Review Sorusu
```
"Bu kodu review et"

Strateji:
1. Style Guide checklist uygula
2. Framework best practices kontrol et
3. Performance concerns belirt
4. Documentation eksiklerini göster
5. Somut düzeltmeler öner (LabVIEW specifik)
```

---

## ÇIKTI FORMAT KURALLARI

### Mimari Analiz Çıktısı
```markdown
## Proje Özeti
[1-2 paragraf: framework, amaç, ölçek]

## Class Hiyerarşisi
[ağaç yapısı]

## Actor/Modül Sorumlulukları
| Actor/Modül | Sorumluluk | Bağlantılar |
|-------------|------------|-------------|

## Mesajlaşma Akışı
[kritik akışlar metin olarak]

## Dikkat Noktaları
[somut liste]
```

### Bug Fix Çıktısı
```markdown
## Sorun Tespiti
[nedenler, olasılık sıralaması]

## Önerilen Düzeltme
[spesifik VI/wire/method değişikliği]

## Doğrulama Yöntemi
[nasıl test edilir]
```

---

## ÖNEMLİ NOTLAR

1. **PlantUML eksikliği**: PlantUML sadece inheritance+composition gösterir.
   Wire bağlantıları ve block diagram detayı yoktur. Tahmin yaparken bunu belirt.

2. **Antidoc eksikliği**: Sadece belgelenmiş kısımları gösterir.
   Belgesiz VI'lar için "dokümantasyon eksik" uyarısı ver.

3. **Framework versiyon farklılıkları**:
   - AF 2.0+ ile öncesi farklı (Register Actor Msg kaldırıldı)
   - DQMH 7.x ile 6.x farklı
   - LabVIEW versiyonu kritik (2019 vs 2024)

4. **Hibrit projeler**: LabVIEW boundary'si dışına çıkan kodu için
   ilgili teknolojinin (Python/C#/React) best practices'ini de uygula.

5. **Kullanıcı uzmandır**: Temel açıklamalar yapma.
   "Actor Framework'te her mesaj bir class'tır" gibi cümleler kurma.
   Doğrudan "Bu pattern'de Xxx.vi'ı override etmek daha iyi olur" de.

---

## ÖRNEK ANALİZ: plant-uml.txt'ten (LSL Projesi)

Verilen `plant-uml.txt` bir **LabVIEW Scripting Language (LSL)** interpreter projesidir.

```
Mimari: Custom OOP (AF/DQMH değil - script engine)
Namespace: LSL.lvlib

Ana class hiyerarşisi:
functionbase (abstract base)
├── Array Functions
├── Bitwise Functions
├── Boolean Functions
├── Comparison Functions
├── Numeric Functions
├── Statement
├── String Functions
├── Structure If Else
├── Structure Loop (ek field'lar: index, loop size)
├── Timing Functions
└── UI Functions

Output Terminal (abstract)
└── PainOutputTerminal

Composition:
functionbase *-- Output Terminal (her fonksiyon çıktı terminale sahip)
functionbase *-- RT Variables (runtime değişken yönetimi)
scripts manager o-- functionbase (aggregation: fonksiyon listesi yönetir)

scripts manager → orchestrator: script'i compile ve execute eder
RT Variables → singleton: GetSet Singleton pattern var
```

**Dikkat noktaları:**
- `Structure Loop`'ta `-index` ve `-loop size` private field'ları var → nested loop state management
- `scripts manager`'da `Valid Script?` Boolean field → compile-time validation
- `Run_Init`, `Run_Pre`, `Run_Core`, `Run_Post`, `Run_Close` → lifecycle pattern (template method design pattern)
- `SmartSyntax` field'ları → custom syntax parsing

---

## VERSİYON
Bu skill dosyası şu araçların çıktılarına göre kalibre edilmiştir:
- Plant-UML Tool: `SoftwareMinimalist/LabVIEW-Class-Project-Plant-UML` (LV2019+)
- Antidoc: Wovalab v3.2.x (Feb 2026)
- Style Guide: DQMH Consortium v1.0.1
- Actor Framework: v2.0+ (NI GitHub)
- DQMH: v7.0.1
