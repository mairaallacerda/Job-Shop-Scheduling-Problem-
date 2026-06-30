# Job Shop Scheduling Problem (JSSP)
### Formulação Disjuntiva MIP resolvida com Gurobi

**Disciplina:** Pesquisa Operacional — CEFET-MG  
**Artigo de referência:** King & Hildebrand (2024) — [arXiv:2407.18111](https://arxiv.org/abs/2407.18111)

---

## 📋 O que é o JSSP?

O **Job Shop Scheduling Problem** é um problema clássico de Otimização Combinatória.  
Dado um conjunto de **n jobs** e **m máquinas**, cada job possui uma sequência fixa de operações que devem ser executadas em máquinas específicas por tempos determinados.

**Objetivo:** encontrar um sequenciamento que minimize o **makespan** (tempo total de conclusão de todos os jobs).

**Por que é difícil?** É um problema NP-difícil — o número de soluções possíveis cresce fatorialmente com o tamanho da instância.

---

## 📄 Artigo de Referência

> **King, B. & Hildebrand, R. (2024).**  
> *Job Shop Scheduling with Integer Programming, Shifting Bottleneck, and Decision Diagrams: A Computational Study.*  
> arXiv:2407.18111  
> 🔗 https://arxiv.org/abs/2407.18111 — **acesso aberto, PDF gratuito**

O artigo compara a formulação disjuntiva MIP (resolvida com Gurobi) com métodos heurísticos avançados para o JSSP.

---

## 📦 Dados da Instância

**Instância utilizada:** FT06 — Fisher & Thompson (1963)
- 6 jobs × 6 máquinas
- Makespan ótimo conhecido: **55**
- **Download:** https://github.com/tamy0612/JSPLIB/blob/master/instances/ft06.txt

> Os dados já estão embutidos no notebook para facilitar a execução.

---

## 🧮 Formulação Matemática (Disjuntiva de Manne, 1960)

**Variáveis:**
- `S[i,j]` ≥ 0 — tempo de início da operação j do job i
- `x[i,j,k,l]` ∈ {0,1} — 1 se operação (i,j) ocorre antes de (k,l) na mesma máquina
- `C_max` ≥ 0 — makespan (variável a minimizar)

**Modelo:**
```
min  C_max

s.a.
(1) S[i,j+1] >= S[i,j] + p[i,j]              ← precedência intra-job
(2) S[k,l]   >= S[i,j] + p[i,j] - V(1-x)    ← disjunção (sem sobreposição)
(3) S[i,j]   >= S[k,l] + p[k,l] - V·x        ← disjunção (sem sobreposição)
(4) C_max    >= S[i,last] + p[i,last]          ← definição do makespan
```

---

## 🗂️ Estrutura do Repositório

```
.
├── jssp_gurobi.ipynb    # Notebook Jupyter com todo o passo a passo
├── relatorio.tex        # Relatório acadêmico em LaTeX
├── relatorio.pdf        # Relatório compilado (gerado a partir do .tex)
├── gantt_ft06.png       # Gráfico de Gantt gerado pelo notebook
└── README.md            # Este arquivo
```

---

## ⚙️ Como executar

### 1. Pré-requisitos

- Python 3.8+
- Licença acadêmica gratuita do Gurobi: https://www.gurobi.com/academia/academic-program-and-licenses/
- VS Code com extensão Jupyter (ou Jupyter Lab)

### 2. Instalar dependências

```bash
pip install gurobipy matplotlib numpy
```

### 3. Executar o notebook

Abra o arquivo `jssp_gurobi.ipynb` no VS Code e execute as células em ordem.  
Cada célula contém comentários explicando o que está sendo feito.

---

## 📊 Resultados Obtidos

| Métrica | Valor |
|---|---|
| Instância | FT06 (6×6) |
| Makespan obtido | **55** |
| Makespan ótimo conhecido | 55 |
| MIP Gap | **0,00%** |
| Tempo de solução | < 1 segundo |
| Variáveis binárias | 90 |
| Variáveis contínuas | 37 |
| Status | **ÓTIMO** |

---

## 🔢 Passo a Passo do Notebook

| Célula | O que faz |
|---|---|
| 1 | Instala as bibliotecas necessárias |
| 2 | Carrega os dados da instância FT06 |
| 3 | Explica a formulação matemática (em Markdown) |
| 4 | Cria o modelo Gurobi e define variáveis |
| 5 | Adiciona todas as restrições do modelo |
| 6 | Resolve o modelo e imprime resultados |
| 7 | Extrai o schedule e exibe em tabela |
| 8 | Gera o gráfico de Gantt |
| 9 | Verifica formalmente a factibilidade da solução |
| 10 | Imprime resumo final |

---

## 📚 Referências

- **King & Hildebrand (2024).** Job Shop Scheduling with Integer Programming... arXiv:2407.18111. https://arxiv.org/abs/2407.18111
- **Manne (1960).** On the Job Shop Scheduling Problem. *Operations Research*, 8(2), 219–223.
- **Fisher & Thompson (1963).** Probabilistic Learning Combinations of Local Job-Shop Scheduling Rules. *Industrial Scheduling*, Prentice-Hall.
- **Ku & Beck (2016).** Mixed Integer Programming Models for Job Shop Scheduling. *Computers & Operations Research*. https://tidel.mie.utoronto.ca/pubs/JSP_CandOR_2016.pdf
- **Instâncias benchmark:** https://github.com/tamy0612/JSPLIB
