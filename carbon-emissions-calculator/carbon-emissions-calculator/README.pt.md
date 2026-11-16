# 🌍 Calculadora de Emissões de Carbono para Transporte Logístico

🇵🇹 Português · [🇬🇧 English](README.md)

![Java](https://img.shields.io/badge/Java-22-orange?logo=openjdk&logoColor=white)
![Gradle](https://img.shields.io/badge/Build-Gradle%208.10-02303A?logo=gradle&logoColor=white)
![JUnit](https://img.shields.io/badge/Testes-JUnit%205-25A162?logo=junit5&logoColor=white)
![Cobertura](https://img.shields.io/badge/Cobertura-JaCoCo-red)
![An%C3%A1lise%20est%C3%A1tica](https://img.shields.io/badge/An%C3%A1lise%20est%C3%A1tica-PMD-blue)
![CI](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![Metodologia](https://img.shields.io/badge/Metodologia-Scrum-informational)

Motor em Java que **calcula e monitoriza as emissões de gases com efeito de estufa (GEE / CO₂e) de cadeias de transporte logístico**. Lê dados de transporte a partir de ficheiros CSV, valida-os, modela cada cadeia como uma sequência de _operações de transporte_ e _operações de hub_, e calcula as emissões segundo uma abordagem **well-to-wheel** (tank-to-wheel + well-to-tank).

> 🎓 **Contexto académico** – Projeto da unidade curricular de Engenharia de Software, Licenciatura em Engenharia Informática · Outubro – Dezembro 2025.
> Desenvolvido em equipa com **Scrum**, com forte foco na qualidade de software: testes automáticos, análise estática, relatórios de cobertura e pipeline de CI.

---

## ✨ O que faz

1. **Carrega e valida dados** – os ficheiros CSV (separados por `;`) são verificados quanto à estrutura (cabeçalhos, número de colunas, modos válidos, intervalos numéricos, unidades esperadas) antes de qualquer cálculo.
2. **Modela cadeias de transporte** – uma `TransportChain` é uma lista de elementos, cada um uma `TransportOperation` (rodoviário, aéreo, marítimo) ou uma `HubOperation` (armazém, centro de distribuição, porto, aeroporto).
3. **Enriquece com dados de referência** – conjuntos de dados secundários fornecem fatores de emissão dos combustíveis, consumos/capacidades dos veículos, fatores de carga por omissão e intensidade energética dos hubs.
4. **Calcula emissões por operação e por cadeia**, devolvendo o total em kg CO₂e e isolando elementos inválidos em vez de falhar.

### Modelo de cálculo

```
Dados de atividade (t·km) = distância × (1 + % retorno em vazio) × (capacidade de carga × fator de carga)
Consumo de energia        = dados de atividade × consumo médio
Emissões de transporte    = consumo × (FE_TTW + FE_WTT)          ← well-to-wheel
Emissões de hub           = fator de emissão × energia consumida
Total da cadeia (GEE)     = Σ operações de transporte + Σ operações de hub
```

As entradas são validadas por intervalos (p. ex. fator de carga ∈ (0, 1], retorno em vazio ∈ [0, 1], distância > 0); as operações inválidas são reportadas e contribuem com 0, em vez de corromperem o total.

## 🧱 Arquitetura

```
app/src/main/java/
├── DataIngest/       # CsvLoader, CsvValidator, TransportDataParser, enums Header/Mode/FuelType/Geography
│   └── SecondaryDataSet/   # Validadores + loaders de combustíveis, hubs, consumos, fatores operacionais
├── TransportChain/   # TransportChain, TransportOperation, HubOperation
└── Calculator/       # CalculateActivityData, ConsumptionEstimator, TOCalculator, HOCalculator, GEECalculator
app/src/main/resources/   # fuels.csv, hubs.csv, operational_factors.csv, transport_consumptions.csv
app/src/test/java/        # Espelha os pacotes principais
```

Decisões de design a destacar: programação para interfaces (`ICsvValidator`, `ICsvLoader`, `ITOCalculations`, `IHOCalculator`…), calculadoras com responsabilidade única e testáveis individualmente, e enums para cabeçalhos/modos do CSV, evitando _magic strings_.

## 🧪 Qualidade e automação

| Prática                 | Detalhe                                                                                                                                                                                                                                          |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Testes unitários**    | JUnit 5 – ~165 casos de teste cobrindo ingestão, validação, _parsing_, cadeias de transporte e todas as calculadoras (valores-limite, entradas inválidas, caminhos de erro)                                                                      |
| **Cobertura de código** | Relatório HTML JaCoCo gerado automaticamente após `test`                                                                                                                                                                                         |
| **Análise estática**    | PMD configurado no Gradle (`./gradlew pmdMain`, regras em `app/src/main/resources/java/custom-pmd-ruleset.xml`)                                                                                                                                  |
| **CI/CD**               | Pipeline de CI (GitHub Actions em `.github/workflows/ci.yml`): build → testes + cobertura JaCoCo → PMD, executada a cada commit. A configuração GitLab CI original (`build` → `test_black_box` → `test_white_box`) mantém-se em `.gitlab-ci.yml` |
| **Build**               | Gradle wrapper, _toolchain_ Java 22                                                                                                                                                                                                              |
| **Processo**            | Scrum – entrega iterativa e incremental, alinhada com os requisitos de qualidade                                                                                                                                                                 |

## 🚀 Como executar

**Requisitos:** JDK 22 (o Gradle usa a _toolchain_ declarada em `app/build.gradle`).

```bash
git clone <url-do-repositório>
cd carbon-emissions-calculator

./gradlew assemble       # compila
./gradlew test           # executa a suite de testes
./gradlew jacocoTestReport
```

O relatório de cobertura fica em `app/build/reports/jacoco/test/html/index.html`.
No Windows usa `gradlew.bat`.

## 🔭 Roadmap

- [ ] Ponto de entrada em linha de comandos (ou REST) para executar cálculos de ponta a ponta a partir de um CSV.
- [ ] Exportação de resultados (CSV / JSON) com detalhe por operação e por cadeia.
- [ ] Suporte para mais âmbitos de contabilização de emissões e mais geografias.

## 👥 Autores

Projeto de grupo desenvolvido com **Scrum**.

- **Maria Leonor Araújo** – [LinkedIn](https://www.linkedin.com/in/leonor-araújo-18155138b)
- **Miguel Ferreira** – [LinkedIn](https://www.linkedin.com/in/miguel-brand%C3%A3o-45a004427)
- **Mariana Barreto** – [LinkedIn](https://www.linkedin.com/in/mariana-barreto-b660221b9)
- **Marcelo Pinto**
- **João Rocha**

## 📄 Licença

Projeto educativo – sem licença definida. Contacta os autores antes de reutilizar o código.
