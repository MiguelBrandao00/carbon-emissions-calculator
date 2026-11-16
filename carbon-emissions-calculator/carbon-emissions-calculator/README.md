# 🌍 Carbon Emissions Calculator for Logistics Transport

[🇵🇹 Português](README.pt.md) · 🇬🇧 English

![Java](https://img.shields.io/badge/Java-22-orange?logo=openjdk&logoColor=white)
![Gradle](https://img.shields.io/badge/Build-Gradle%208.10-02303A?logo=gradle&logoColor=white)
![JUnit](https://img.shields.io/badge/Tests-JUnit%205-25A162?logo=junit5&logoColor=white)
![Coverage](https://img.shields.io/badge/Coverage-JaCoCo-red)
![Static Analysis](https://img.shields.io/badge/Static%20analysis-PMD-blue)
![CI](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![Methodology](https://img.shields.io/badge/Methodology-Scrum-informational)

A Java engine that **calculates and monitors greenhouse-gas (GHG / CO₂e) emissions of logistics transport chains**. It ingests transport data from CSV files, validates it, models each chain as a sequence of _transport operations_ and _hub operations_, and computes emissions using a **well-to-wheel** approach (tank-to-wheel + well-to-tank).

> 🎓 **Academic context** – Software Engineering course project, BSc in Computer Engineering · October – December 2025.
> Developed as a team using **Scrum**, with a strong emphasis on software quality: automated tests, static analysis, coverage reports and a CI pipeline.

---

## ✨ What it does

1. **Loads & validates data** – semicolon-delimited CSV files are checked for structure (headers, number of columns, valid modes, numeric ranges, expected units) before any calculation happens.
2. **Models transport chains** – a `TransportChain` is a list of elements, each one either a `TransportOperation` (road, air, sea) or a `HubOperation` (warehouse, distribution centre, port, airport).
3. **Enriches with reference data** – secondary datasets provide fuel emission factors, vehicle consumption/capacity, default load factors and hub energy intensity.
4. **Calculates emissions per operation and per chain**, reporting a total in kg CO₂e and isolating invalid elements instead of crashing.

### Calculation model

```
Activity data (t·km) = distance × (1 + empty-return %) × (load capacity × load factor)
Energy consumption   = activity data × average consumption
Transport emissions  = consumption × (EF_TTW + EF_WTT)         ← well-to-wheel
Hub emissions        = emission factor × energy consumed
Chain total (GEE)    = Σ transport operations + Σ hub operations
```

Inputs are range-checked (e.g. load factor ∈ (0, 1], empty-return ∈ [0, 1], distance > 0); invalid operations are reported and contribute 0 rather than corrupting the total.

## 🧱 Architecture

```
app/src/main/java/
├── DataIngest/       # CsvLoader, CsvValidator, TransportDataParser, Header/Mode/FuelType/Geography enums
│   └── SecondaryDataSet/   # Validators + loaders for fuels, hubs, consumptions, operational factors
├── TransportChain/   # TransportChain, TransportOperation, HubOperation
└── Calculator/       # CalculateActivityData, ConsumptionEstimator, TOCalculator, HOCalculator, GEECalculator
app/src/main/resources/   # fuels.csv, hubs.csv, operational_factors.csv, transport_consumptions.csv
app/src/test/java/        # Mirrors the main packages
```

Design decisions worth noting: programming to interfaces (`ICsvValidator`, `ICsvLoader`, `ITOCalculations`, `IHOCalculator`…), single-responsibility calculators that are individually testable, and enums for CSV headers/modes to avoid "magic strings".

## 🧪 Quality & automation

| Practice            | Details                                                                                                                                                                                                                                      |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Unit testing**    | JUnit 5 – ~165 test cases covering ingestion, validation, parsing, transport chains and every calculator (boundary values, invalid input, error paths)                                                                                       |
| **Code coverage**   | JaCoCo HTML report generated automatically after `test`                                                                                                                                                                                      |
| **Static analysis** | PMD configured in Gradle (`./gradlew pmdMain`, rules in `app/src/main/resources/java/custom-pmd-ruleset.xml`)                                                                                                                                |
| **CI/CD**           | CI pipeline (GitHub Actions, `.github/workflows/ci.yml`): build → tests + JaCoCo coverage → PMD, triggered on every commit. The original GitLab CI configuration (`build` → `test_black_box` → `test_white_box`) is kept in `.gitlab-ci.yml` |
| **Build tool**      | Gradle wrapper, Java 22 toolchain                                                                                                                                                                                                            |
| **Process**         | Scrum – iterative, incremental delivery aligned with quality requirements                                                                                                                                                                    |

## 🚀 Getting started

**Requirements:** JDK 22 (Gradle will use the toolchain declared in `app/build.gradle`).

```bash
git clone <repository-url>
cd carbon-emissions-calculator

./gradlew assemble       # compile
./gradlew test           # run the test suite
./gradlew jacocoTestReport
```

The coverage report is generated at `app/build/reports/jacoco/test/html/index.html`.
On Windows use `gradlew.bat`.

## 🔭 Roadmap

- [ ] Command-line (or REST) entry point to run calculations from a CSV file end-to-end.
- [ ] Export results (CSV / JSON) with per-operation and per-chain breakdowns.
- [ ] Support for additional emission-accounting scopes and more geographies.

## 👥 Authors

Group project developed with **Scrum**.

- **Maria Leonor Araújo** – [LinkedIn](https://www.linkedin.com/in/leonor-araújo-18155138b)
- **Miguel Ferreira** – [LinkedIn](https://www.linkedin.com/in/miguel-brand%C3%A3o-45a004427)
- **Mariana Barreto** – [LinkedIn](https://www.linkedin.com/in/mariana-barreto-b660221b9)
- **Marcelo Pinto**
- **João Rocha**

## 📄 License

Educational project – no license specified. Please contact the authors before reusing the code.
