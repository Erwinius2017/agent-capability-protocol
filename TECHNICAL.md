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

ACP nie je náhrada existujúcich protokolov, ale ich **nadstavba a doplnok**. Vysokotúrovňová interoperabilita je plánovaná nasledovne:

| Protokol | Spôsob integrácie |
|----------|-------------------|
| **MCP** | ACP runtime môže využívať MCP na volanie externých nástrojov. MCP adaptér prekladá interné capability do MCP toolov. |
| **Nuwa ACP (.acp.yaml)** | Nuwa formát pre popis capability balíčkov môže byť jedným zo vstupných formátov pre import do capability graph. |
| **A2A** | Pre komunikáciu s inými agentmi môže ACP využívať A2A protokol. A2A adaptér prekladá interné dopyty do A2A správ. |
| **ANP** | Pre objavovanie agentov v otvorenom internete môže ACP využívať ANP. |

Konkrétna implementácia adaptérov (mapovanie, preklad správ, riešenie konfliktov) je zatiaľ súčasťou uzavretého runtime a nie je verejne dokumentovaná.

```ascii
┌───────────────────────┐
│    ACP Runtime        │
│  (uzavreté jadro)     │
└─────────┬─────────────┘
          │
┌─────────▼──────────────┐
│   Adaptéry (rozhranie) │
├───────────┬────────────┤
│ MCP │ A2A │ Nuwa │ ANP │
└─────┴─────┴──────┴─────┘
```

---

## 4. Poznámka k otvorenosti a spolupráci

Tento dokument popisuje koncepty a víziu ACP. Všetky príklady sú ilustratívne. 
Finálny kód ACP bude open-source pod MIT licenciou.

Ak máte záujem o technickú spoluprácu, diskusiu, alebo partnerstvo, pozrite si [CONTRIBUTING.md](CONTRIBUTING.md), kde nájdete všetky informácie o tom, ako sa zapojiť, a kontakt na autora.
