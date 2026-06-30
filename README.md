# Job Shop Scheduling Problem (JSSP)
### Formulação Disjuntiva MIP — Resolvido com Gurobi

**Disciplina:** Otimização Combinatória — CEFET-MG  
**Professor:** Alisson Marques da Silva  
**Artigo de referência:** King & Hildebrand (2024) — [arXiv:2407.18111](https://arxiv.org/abs/2407.18111)

---

## O que é o JSSP?

O **Job Shop Scheduling Problem (JSSP)** é um problema clássico de Otimização Combinatória, classificado como **NP-difícil**. Ele modela situações reais de manufatura onde:

- Há **n jobs** (tarefas) e **m máquinas**
- Cada job possui uma **sequência fixa de operações**, cada uma exigindo uma máquina específica por um tempo determinado
- Cada máquina processa **apenas um job por vez**
- As operações não podem ser interrompidas (**não-preempção**)

**Objetivo:** encontrar o sequenciamento que minimize o **makespan** — o tempo total até que todos os jobs sejam concluídos.

---

## Artigo de Referência

> **King, B. & Hildebrand, R. (2024).**  
> *Job Shop Scheduling with Integer Programming, Shifting Bottleneck, and Decision Diagrams: A Computational Study.*  
> arXiv:2407.18111  
> 🔗 https://arxiv.org/abs/2407.18111 — **acesso aberto, PDF gratuito**

O artigo compara três abordagens para resolver o JSSP:
1. **Programação Inteira (MIP)** com Gurobi — formulação disjuntiva de Manne (1960)
2. **Shifting Bottleneck** — heurística decomposicional clássica
3. **Decision Diagrams** — abordagem baseada em diagramas de decisão

Este trabalho reproduz a abordagem **1 (MIP com Gurobi)**.

---

## Formulação Matemática (Disjuntiva de Manne, 1960)

**Variáveis de decisão:**

| Variável | Tipo | Significado |
|---|---|---|
| `S[i,j]` | Contínua ≥ 0 | Tempo de **início** da operação j do job i |
| `x[i,j,k,l]` | Binária {0,1} | 1 se operação (i,j) ocorre **antes** de (k,l) na mesma máquina |
| `C_max` | Contínua ≥ 0 | **Makespan** — variável a minimizar |

**Modelo:**

```
min  C_max

s.a.
(1) S[i,j+1] >= S[i,j] + p[i,j]              → precedência intra-job
(2) S[k,l]   >= S[i,j] + p[i,j] - V(1-x)    → disjunção: sem sobreposição
(3) S[i,j]   >= S[k,l] + p[k,l] - V·x        → disjunção: sem sobreposição
(4) C_max    >= S[i,last] + p[i,last]          → definição do makespan
```

onde `V = Σ p[i,j]` é o Big-M (soma de todos os tempos de processamento).

**Como a disjunção funciona:**  
- Se `x=1` → restrição (2) fica ativa: (i,j) precede (k,l) na máquina  
- Se `x=0` → restrição (3) fica ativa: (k,l) precede (i,j) na máquina  
- O solver decide o valor de cada `x` para minimizar o makespan

---

## Instância Utilizada

**FT06 — Fisher & Thompson (1963)**

| Parâmetro | Valor |
|---|---|
| Jobs | 6 |
| Máquinas | 6 |
| Operações por job | 6 |
| Total de operações | 36 |
| **Makespan ótimo conhecido** | **55** |

Download dos dados: https://github.com/tamy0612/JSPLIB

---

## Resultados Obtidos

| Métrica | Valor |
|---|---|
| Status | **ÓTIMO** |
| Makespan obtido | **55** |
| Makespan ótimo conhecido | 55 |
| MIP Gap | **0,00%** |
| Tempo de execução | **0,474 s** |
| Nós Branch-and-Bound | 1 |
| Variáveis binárias | 90 |
| Variáveis contínuas | 37 |
| Restrições totais | 216 |

✅ Solução ótima confirmada — resultado idêntico ao reportado no artigo de referência.

---

## Estrutura do Repositório

```
📁 PO_trabFinal/
├── 📓 jssp_gurobi.ipynb     # Notebook Jupyter com toda a implementação
├── 📄 relatorio.tex         # Relatório acadêmico em LaTeX
├── 📄 relatorio.pdf         # Relatório compilado (gerado a partir do .tex)
├── 🖼️  gantt_ft06.png        # Gráfico de Gantt gerado pelo notebook
├── 📋 README.md             # Este arquivo
└── 📁 instances/            # Instâncias benchmark (formato JSPLIB)
    ├── ft06                 # 6 jobs × 6 máquinas — ótimo = 55
    ├── ft10                 # 10 jobs × 10 máquinas — ótimo = 930
    ├── ft20                 # 20 jobs × 5 máquinas — ótimo = 1165
    ├── la01 ... la10        # Família Lawrence (10×5 a 15×10)
    ├── abz5, abz6           # Família Adams-Balas-Zawack (10×10)
    └── ...                  # Demais instâncias disponíveis
```

---

## Como Executar

### 1. Pré-requisitos

- Python 3.8 ou superior
- Licença acadêmica gratuita do Gurobi:  
  https://www.gurobi.com/academia/academic-program-and-licenses/
- VS Code com a extensão **Jupyter** instalada

### 2. Clonar o repositório

```bash
git clone https://github.com/seu-usuario/PO_trabFinal.git
cd PO_trabFinal
```

### 3. Criar ambiente virtual e instalar dependências

```bash
python3 -m venv .venv
source .venv/bin/activate        # Linux/Mac
# ou: .venv\Scripts\activate     # Windows

pip install gurobipy matplotlib numpy
```

### 4. Executar o notebook

Abra o arquivo `jssp_gurobi.ipynb` no VS Code e execute as células em ordem com **Shift+Enter**, ou clique em **Executar Tudo**.

### 5. Trocar de instância (opcional)

Na célula 2 do notebook, altere apenas esta linha:

```python
INSTANCIA = "ft06"   # troque para: ft10, ft20, la01, la02, abz5, abz6...
```

Execute o notebook do início. O código carrega automaticamente qualquer instância da pasta `instances/`.

---

## Ambiente Computacional

| Componente | Especificação |
|---|---|
| Sistema Operacional | Ubuntu 24.04.4 LTS (via WSL2) |
| Processador | AMD Ryzen 7 5700U |
| Threads | 16 lógicas |
| Python | 3.12.3 |
| Gurobi | 13.0.2 (licença acadêmica) |
| gurobipy | 13.0.2 |
| matplotlib | 3.11.0 |
| numpy | 2.5.0 |

---

## Referências

- **King & Hildebrand (2024).** Job Shop Scheduling with Integer Programming, Shifting Bottleneck, and Decision Diagrams. arXiv:2407.18111. https://arxiv.org/abs/2407.18111

- **Manne, A.S. (1960).** On the Job Shop Scheduling Problem. *Operations Research*, 8(2), 219–223.

- **Fisher, H. & Thompson, G.L. (1963).** Probabilistic Learning Combinations of Local Job-Shop Scheduling Rules. *Industrial Scheduling*, Prentice-Hall.

- **Ku, W.-Y. & Beck, J.C. (2016).** Mixed Integer Programming Models for Job Shop Scheduling: A Computational Analysis. *Computers & Operations Research*, 73, 165–173. https://tidel.mie.utoronto.ca/pubs/JSP_CandOR_2016.pdf

- **Instâncias benchmark:** https://github.com/tamy0612/JSPLIB