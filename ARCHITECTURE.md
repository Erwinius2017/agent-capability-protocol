# 🏗️ Architektúra ACP (prehľad) - WORK IN PROGRESS

ACP je navrhnutý ako modulárny runtime s týmito hlavnými vrstvami:

## 1. Vstupná brána (Gateway)
- Príjma požiadavky od ľudí, agentov a systémov
- Zabezpečuje autentifikáciu a smerovanie

## 2. Riadiace jadro (Core)
- **Intent Understanding**: Pochopenie zámeru a kontextu
- **Capability Graph**: Mapovanie zámeru na sieť schopností
- **Workflow Engine**: Orchestrácia krokov s podporou kompenzácií

## 3. Podporné systémy
- **Cost Model**: Dynamický odhad nákladov, latencie a rizika
- **State Context**: Zdieľaná pamäť medzi agentom a runtime
- **Policy & Governance**: Priebežné overovanie pravidiel
- **Audit & Evidence**: Nemenný záznam všetkých udalostí

## 4. Dátová vrstva
- Append-only úložisko s kryptografickým reťazením
- Jednotné API pre dopytovanie naprieč úložiskami

## 5. Ekosystém
- Komunikácia medzi ACP inštanciami
- Zdieľanie politík a skúseností
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                       EKOSYSTÉM (Pilier 9)                                       │
│              (prostredie, v ktorom žijú všetky ACP inštancie)                    │
│  ┌─────────────────────────────────────────────────────────────────────────────┐ │
│  │                         VONKAJŠÍ SVET                                       │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │ │
│  │  │ Ľudia    │  │ Agenti   │  │ API      │  │ Dátové   │  │ Iné ACP      │   │ │
│  │  │ (CEO,    │  │ (iné     │  │ služby   │  │ zdroje   │  │ inštancie    │   │ │
│  │  │compliance│  │ systémy) │  │ (SAP,    │  │ (DB,     │  │ (iné         │   │ │
│  │  │ klienti) │  │          │  │ AWS, ...)│  │ súbory)  │  │ organizácie) │   │ │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────────┘   │ │
│  └───────┼─────────────┼─────────────┼─────────────┼─────────────┼─────────────┘ │
│          ▼             ▼             ▼             ▼             ▼               │
│  ┌─────────────────────────────────────────────────────────────────────────────┐ │
│  │                    ACP GATEWAY (Rozhranie)                                  │ │
│  │  • Jednotný vstup • Autentifikácia • Load balancing                         │ │
│  └─────────────────────────────────────────────────────────────────────────────┘ │
│                                   │                                              │
│                                   ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐ │
│  │                          RIADIACE JADRO (ACP Core)                          │ │
│  │ V jadre prebiehajú tri hlavné kroky, ktoré sú znázornené v diagrame nižšie  │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  KROK 1: POROZUMENIE ZÁMERU                                          │   │ │
│  │  │  (Intent Understanding)                                              │   │ │
│  │  │  • Čo chce používateľ dosiahnuť?                                     │   │ │
│  │  │  • Aký je kontext? (kto, kde, kedy)                                  │   │ │
│  │  │  • Aké sú obmedzenia? (cena, čas, riziko)                            │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                   │                                         │ │
│  │                                   ▼                                         │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  KROK 2: CAPABILITY GRAPH (Pilier 1)                                 │   │ │
│  │  │  • Nájdenie optimálnej cesty k cieľu                                 │   │ │
│  │  │  • Identifikácia závislostí medzi schopnosťami                       │   │ │
│  │  │  • Príprava alternatívnych ciest                                     │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                   │                                         │ │
│  │                                   ▼                                         │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  KROK 3: WORKFLOW ENGINE (Pilier 2)                                  │   │ │
│  │  │  • Spustenie DAG                                                     │   │ │
│  │  │  • Paralelné vykonávanie                                             │   │ │
│  │  │  • Retry mechanizmy a kompenzácie                                    │   │ │
│  │  │  • Spracovanie čiastočných zlyhaní                                   │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  └─────────────────────────────────────────────────────────────────────────────┘ │
│                                   │                                              │
│                                   ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐ │
│  │                     PODPORNÉ SYSTÉMY (Piliere 3-8)                          │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 3: SEMANTIC COST MODEL                                       │   │ │
│  │  │  • Odhad latencie, ceny, spoľahlivosti a rizika                      │   │ │
│  │  │  • Dynamické prepočty podľa aktuálnych podmienok                     │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 4: STATEFUL EXECUTION CONTEXT                                │   │ │
│  │  │  • Zdieľaná pamäť medzi agentom a runtime                            │   │ │
│  │  │  • Historické dáta a predikcia budúcich krokov                       │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 5: POLICY & GOVERNANCE LAYER                                 │   │ │
│  │  │  ┌──────────────────┐  ┌──────────────────┐                          │   │ │
│  │  │  │  POLICY ENGINE   │  │  AUDIT &         │                          │   │ │
│  │  │  │  • Pravidlá      │  │  EVIDENCE        │                          │   │ │
│  │  │  │  • Jurisdikcie   │  │  • Nemenný záznam│                          │   │ │
│  │  │  │  • Eskalácie     │  │  • Hash reťaz    │                          │   │ │
│  │  │  └──────────────────┘  │  • Export        │                          │   │ │
│  │  │                        └──────────────────┘                          │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 6: EVOLÚCIA                                                  │   │ │
│  │  │  • Učenie sa z minulosti                                             │   │ │
│  │  │  • Dolaďovanie cost modelu a capability grafu                        │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 7: REPRODUKCIA                                               │   │ │
│  │  │  • Šírenie skúseností medzi inštanciami                              │   │ │
│  │  │  • Vznik "druhov" agentov                                            │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                             │ │
│  │  ┌──────────────────────────────────────────────────────────────────────┐   │ │
│  │  │  PILIER 8: SEBAUVEDOMENIE                                            │   │ │
│  │  │  • Monitoring vlastného zdravia                                      │   │ │
│  │  │  • Predikcia zlyhaní                                                 │   │ │
│  │  │  • Preventívne opatrenia                                             │   │ │
│  │  └──────────────────────────────────────────────────────────────────────┘   │ │
│  └─────────────────────────────────────────────────────────────────────────────┘ │
│                                   │                                              │
│                                   ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐ │
│  │                     DÁTOVÁ VRSTVA (Unified Data Fabric)                     │ │
│  │  • Append-only event store                                                  │ │
│  │  • Kryptografické reťazenie                                                 │ │
│  │  • Register úložísk                                                         │ │
│  │  • Jednotné dopytovacie API                                                 │ │
│  └─────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                  │
│        ◀──────────────────────  VÝSTUPY  ───────────────────────▶               │
│  ┌─────────────────────┐  ┌─────────────────────┐  ┌─────────────────────────┐   │
│  │     Výsledok        │  │    Report pre       │  │     Alert pre           │   │
│  │     (splnený cieľ)  │  │      manažéra       │  │      compliance         │   │
│  └─────────────────────┘  └─────────────────────┘  └─────────────────────────┘   │
│  ┌─────────────────────┐                                                         │
│  │      Audit trail    │                                                         │
│  │     pre regulátora  │                                                         │
│  └─────────────────────┘                                                         │
│                                                                                  │
└──────────────────────────────────────────────────────────────────────────────────┘
```
