# 🔧 Technické detaily ACP

Tento dokument obsahuje technické koncepty a príklady, ktoré ilustrujú víziu ACP. **Neobsahuje žiadne implementačné detaily, algoritmy ani internú logiku runtime.** Slúži na pochopenie rozhraní a možností, nie ako návod na stavbu konkurenčného riešenia.

---

## 1. Príklady vstupno-výstupných kontraktov

Nasledujúce príklady ilustrujú, ako by mohla vyzerať komunikácia s ACP runtime. Sú to **ilustratívne šablóny**, nie finálna špecifikácia.

### 1.1 Jednoduchý dopyt (intent)

```json
{
  "request": {
    "intent": "book_flight",
    "passenger_id": "PASS-12345",
    "destination": "LHR",
    "date": "2026-03-15",
    "constraints": {
      "max_price": 500,
      "preferred_airline": "SK"
    }
  },
  "response": {
    "status": "confirmed",
    "booking_reference": "ACP-7F3A-9B2C",
    "price": 450.00,
    "details": {
      "flight": "SK123",
      "departure": "2026-03-15T08:30",
      "arrival": "2026-03-15T10:45"
    }
  }
}
```

### 1.2 Požiadavka s kompenzáciou

```json
{
  "request": {
    "intent": "book_hotel_and_flight",
    "passenger_id": "PASS-12345",
    "hotel": {
      "city": "London",
      "check_in": "2026-03-15",
      "check_out": "2026-03-18"
    },
    "flight": {
      "destination": "LHR",
      "date": "2026-03-15"
    }
  },
  "response": {
    "status": "partial_success",
    "message": "Hotel booked, flight failed – automatic compensation initiated",
    "details": {
      "hotel_booking": "HOT-9876",
      "flight_status": "failed",
      "compensation": "hotel_cancelled"
    }
  }
}
```

*Poznámka: Interná logika, ktorá vedie k "partial_success" a kompenzáciám, je súčasťou uzavretého runtime a nie je predmetom tohto dokumentu.*

---

## 2. Metamodel capability graph (high-level typy)

Capability graph je sieť schopností, ktoré môže agent využiť. Na najvyššej úrovni rozlišujeme tieto typy capability:

| Typ | Popis | Príklad |
|-----|-------|---------|
| **Transaction** | Mení stav, má definovanú kompenzáciu | `book_flight`, `transfer_money` |
| **Query** | Len číta, nemení stav, idempotentné | `get_flight_options`, `check_balance` |
| **Orchestration** | Spája viacero capabilities do vyššieho celku | `book_trip` (zahŕňa let + hotel) |
| **Utility** | Pomocné, bez vlastného stavu | `validate_email`, `format_address` |
| **Compensation** | Špeciálny typ pre vrátenie transakcií | `cancel_booking`, `refund_payment` |

Tieto typy sa môžu kombinovať a vytvárať tak zložitejšie schopnosti. Samotná implementácia grafu (ako sú uzly ukladané, vyhľadávané a optimalizované) je súčasťou interného runtime.

---

## 3. Interoperabilita s existujúcimi protokolmi

### 3.1 Princíp protocol stackingu

ACP nie je náhrada existujúcich protokolov, ale ich **nadstavba**. Vychádzame z princípu "protocol stacking", kde každý protokol pokrýva inú vrstvu:

| Vrstva | Protokol | Rola v ACP architektúre |
|--------|----------|-------------------------|
| **Tool access** | MCP | ACP runtime volá externé nástroje a API cez MCP adaptér. MCP server poskytuje jednotné rozhranie k databázam, súborom a službám. |
| **Agent-to-agent komunikácia** | A2A, ACP (IBM) | Pre priamu komunikáciu s inými agentmi (mimo ACP ekosystému) môže ACP využívať A2A alebo IBM ACP protokol. |
| **Objavovanie agentov** | ANP | Pre nájdenie agentov v otvorenom internete možno využiť ANP s W3C DID identitami. |
| **Capability balíčky** | Nuwa NIP-7 | .acp.yaml formát slúži ako jeden zo vstupných formátov pre import capability balíčkov. |

### 3.2 ACP adaptéry

Každý adaptér je zodpovedný za:
- **Mapovanie** – transformáciu medzi externým formátom a interným capability graph
- **Preklad správ** – konverziu formátov (JSON-RPC, REST, JSON-LD)
- **Riešenie konfliktov** – ak viacero protokolov ponúka rovnakú funkcionalitu

Adaptéry sú navrhnuté ako **vymeniteľné moduly**. To umožňuje:
- Pridávať podporu nových protokolov bez zmeny jadra
- V prípade potreby použiť viacero adaptérov súčasne
- Izolovať závislosti na externých protokoloch

### 3.3 Príklad: MCP adaptér

MCP používa klient-server architektúru s JSON-RPC 2.0. ACP MCP adaptér:

1. **Discover** – načíta z MCP servera zoznam dostupných toolov (cez `tools/list`)
2. **Map** – pretransformuje MCP tooly na interné capability (podľa typológie z časti 2)
3. **Translate** – keď ACP potrebuje zavolať tool, adaptér vytvorí MCP request (`tools/call`)
4. **Handle response** – spracuje odpoveď a vráti ju do capability graph

```ascii
┌────────────┐      ┌────────────┐     ┌────────────┐
│ ACP Core   │────▶│ MCP adaptér│────▶│ MCP server │
│            │◀────│            │◀────│            │
└────────────┘      └────────────┘     └────────────┘
      │                   │                   │
 capability            JSON-RPC            tool
    graph              preklad             exec
```

### 3.4 Príklad: Nuwa adaptér (.acp.yaml)

Nuwa NIP-7 definuje `.acp.yaml` formát pre capability balíčky. ACP Nuwa adaptér:

1. **Parse** – načíta YAML súbor a validuje ho podľa schémy
2. **Resolve dependencies** – identifikuje závislosti medzi capability
3. **Transform** – vytvorí z neho interný capability graph (typy: Transaction, Query, atď.)
4. **Register** – sprístupní capability pre runtime

### 3.5 Overenie interoperability

Interoperabilitu overujeme na troch úrovniach:

| Úroveň | Metóda | Čo overujeme |
|--------|--------|--------------|
| **Syntaktická** | Testovanie formátov správ | Či adaptér správne parsuje a generuje správy v očakávanom formáte |
| **Sémantická** | Testovanie významu | Či preklad zachováva význam (napr. MCP tool → capability) |
| **Protokolová** | Testovanie sekvencií | Či adaptér dodržuje protokol (napr. MCP lifecycle) |

### 3.6 Poznámka k implementácii

Konkrétna implementácia adaptérov (mapovacie tabuľky, prekladové pravidlá, optimalizácie) je súčasťou uzavretého runtime a nie je verejne dokumentovaná. Tento dokument popisuje len **rozhrania a princípy**, ktoré sú nevyhnutné pre pochopenie interoperability.

## 4. Poznámka k otvorenosti a spolupráci

Tento dokument popisuje koncepty a víziu ACP. Všetky príklady sú ilustratívne. 
Finálny kód ACP bude open-source pod MIT licenciou.

Ak máte záujem o technickú spoluprácu, diskusiu, alebo partnerstvo, pozrite si [CONTRIBUTING.md](CONTRIBUTING.md), kde nájdete všetky informácie o tom, ako sa zapojiť, a kontakt na autora.
