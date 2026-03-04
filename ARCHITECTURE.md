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
---
### 🧭 Postavenie ACP v ekosystéme agentných protokolov

V súčasnosti existuje niekoľko projektov, ktoré riešia špecifické vrstvy komunikácie v ekosystéme AI agentov. Pre pochopenie jedinečnosti nášho ACP je kľúčové uvedomiť si, že **ACP nepracuje na rovnakej úrovni ako tieto projekty, ale o úroveň vyššie**.


| Protokol | Hlavné zameranie |
|----------|------------------|
| **MCP (Model Context Protocol)** | Pripojenie agenta k nástrojom a dátam („USB-X pre AI“) |
| **A2A (Agent-to-Agent)** | Priama komunikácia a delegovanie úloh medzi agentmi („Ethernet pre agentov“) |
| **ACP (Agent Communication Protocol) od IBM/BeeAI** | Štruktúrovaná komunikácia v riadených prostrediach |
| **ANP (Agent Network Protocol)** | Objavovanie a bezpečná komunikácia v otvorenom, decentralizovanom internete |
| **AP2 (Agent Payment Protocol)** | Bezpečné vykonávanie platieb medzi agentmi |

Všetky tieto projekty riešia primárne **konektivitu a výmenu správ**.

Náš **ACP (Agent Capability Protocol)** rieši niečo zásadne odlišné: **vnútorný život, správanie a zodpovednosť agenta**. Je to runtime, ktorý agentovi dáva „telo, pamäť, svedomie a spoločenstvo“.

Zatiaľ čo iné projekty odpovedajú na otázky typu:
- „Ako zavolám API?“ (MCP)
- „Ako pošlem úlohu inému agentovi?“ (A2A, ACP)
- „Ako nájdem agenta na internete?“ (ANP)
- „Ako zaplatím inému agentovi?“ (AP2)

**ACP odpovedá na otázky:**
- „Ako sa agent rozhodne, ktorú cestu použiť?“ (Capability Graph, Cost Model)
- „Ako zaručím, že sa zložitý proces dokončí, alebo vráti späť?“ (Workflow Engine, kompenzácie)
- „Ako agent nesie zodpovednosť za svoje činy v súlade s jurisdikciou?“ (Policy & Governance)
- „Ako sa agent učí z vlastných chýb a zlepšuje sa?“ (Evolúcia)
- „Ako agenti, ako druh, odovzdávajú skúsenosti?“ (Reprodukcia)
- „Ako agent monitoruje vlastné zdravie a predchádza zlyhaniam?“ (Sebauvedomenie)
- „Ako agenti vytvárajú spoločenstvá, trhy a kultúru?“ (Ekosystém)

ACP preto nie je náhradou za MCP, A2A či ANP. Naopak, je ich **prirodzeným doplnkom a nadstavbou**. ACP runtime vo svojej vnútornej orchestrácii môže pokojne využívať MCP na volanie nástrojov, A2A na komunikáciu s partnermi, ANP na objavovanie nových služieb a AP2 na platby.

| Vrstva | Riešenie | Príklad otázky |
|--------|----------|----------------|
| **Konektivita a výmena správ** | MCP, A2A, ACP (IBM/BeeAI), ANP, AP2 | Ako sa spojiť, komunikovať, odovzdať úlohu, zaplatiť? |
| **Život a zodpovednosť agenta** | **ACP (Agent Capability Protocol)** | Ako sa rozhodnúť, zaručiť, zodpovedať, poučiť sa, spolupracovať? |

---

⚖ Etický fundament (Asimovove zákony)

Architektúra ACP je od základov navrhovaná s ohľadom na etické princípy. Inšpiráciou sú tri (a nultý) zákony robotiky Isaaca Asimova, ktoré vnímame ako prirodzený rámec pre zodpovedné správanie agentov:

    **Nultý zákon:** Agent nesmie ublížiť ľudstvu, alebo svojou nečinnosťou dopustiť, aby bolo ľudstvu ublížené.

    **Prvý zákon:** Agent nesmie ublížiť človeku, alebo svojou nečinnosťou dopustiť, aby bolo človeku ublížené.

    **Druhý zákon:** Agent musí poslúchnuť človeka, okrem prípadov, keď je to v rozpore s prvým zákonom alebo nultým zákonom.

    **Tretí zákon:** Agent sa musí chrániť pred poškodením, okrem prípadov, keď je to v rozpore s nultým zákonom, alebo prvým zákonom, alebo druhým zákonom, alebo akoukoľvek kombináciou z množiny tvorenej nultým zákonom, prvým zákonom, druhým zákonom.

Tieto zákony nie sú v ACP implementované ako pevný kód, ale ako súbor **najvyšších politík (meta-policies)** , ktoré sú premietnuté do všetkých nižších vrstiev – od capability graph (zákaz škodlivých akcií) cez workflow engine (kompenzácie škôd) až po audit (dohľad nad dodržiavaním).
