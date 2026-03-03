# 📖 Slovník pojmov ACP

Prehľad kľúčových konceptov a ich význam v rámci Agent Capability Protocol.

| Pojem | Význam |
|-------|--------|
| **Capability Graph** | Sieť schopností, ktorú môže agent využiť. Uzly sú schopnosti, hrany sú závislosti a alternatívy. |
| **Compensation** | Mechanizmus na vrátenie zmien, ak neskorší krok zlyhá (Saga pattern). |
| **Cost Model** | Dynamický odhad latencie, financií, spoľahlivosti a rizika pre každú akciu. |
| **DAG Execution** | Vykonávanie krokov v poradí určenom grafom závislostí, s možnosťou paralelizácie. |
| **Ekosystém** | Prostredie, v ktorom žijú a komunikujú viaceré ACP inštancie. Zahŕňa zdieľanie politík, skúseností a vznik "druhov" agentov. |
| **Evolúcia** | Schopnosť ACP učiť sa z minulých interakcií, dolaďovať cost model a optimalizovať cesty v capability grafe. |
| **Policy as Code** | Pravidlá (kto, čo, kde, kedy) zapísané v strojovo čitateľnom jazyku (napr. Rego, Cedar). |
| **Reprodukcia** | Šírenie skúseností a "genetickej informácie" medzi inštanciami ACP, vznik špecializovaných druhov agentov. |
| **Saga pattern** | Architektonický vzor pre riadenie distribuovaných transakcií pomocou kompenzačných operácií. |
| **Sebauvedomenie** | Monitorovanie vlastného zdravia, predikcia zlyhaní a spúšťanie preventívnych opatrení. |
| **Stateful Context** | Zdieľaná, vyvíjajúca sa pamäť medzi agentom a ACP runtime. Umožňuje kontinuitu bez opakovaného posielania histórie. |
| **Unified Data Fabric** | Jednotná dátová vrstva, ktorá poskytuje logický pohľad na fyzicky roztrúsené úložiská (blockchain, databázy, hybrid). |
| **Workflow Engine** | Srdce orchestrácie – spúšťa DAG, riadi paralelné vykonávanie, retry mechanizmy a kompenzácie pri zlyhaní. |

---

✍️ **Autor:** Branislav Šteffek  
📅 **Dátum:** 3. marec 2026  
📢 **Licencia:** MIT
