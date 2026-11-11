<div align="center">

# ⚡ Automação ESG com PL/SQL

**Banco de dados Oracle que monitora o consumo de energia de equipamentos IoT por setor e gera alertas de desperdício automaticamente com triggers PL/SQL.**

![Oracle](https://img.shields.io/badge/Oracle-Database-F80000?logo=oracle&logoColor=white)
![PL/SQL](https://img.shields.io/badge/PL%2FSQL-Triggers-F80000)
![SQL](https://img.shields.io/badge/SQL-DDL_%2F_DML-336791)
![ESG](https://img.shields.io/badge/ESG-Ambiental-2E7D32)

</div>

---

## 📌 Sobre o projeto

Atividade da **FIAP (Análise e Desenvolvimento de Sistemas)** sobre automação de processos ESG.
O objetivo é tirar a fiscalização de consumo de energia das planilhas: as próprias regras de sustentabilidade ficam **dentro do banco**, e cada leitura, falha ou mudança de meta dispara o alerta certo sem depender da aplicação.

## 🗂️ Modelo de dados

```mermaid
erDiagram
    SETOR ||--o{ EQUIPAMENTO_IOT : possui
    EQUIPAMENTO_IOT ||--o{ LEITURA_CONSUMO : registra
    EQUIPAMENTO_IOT ||--o{ ALERTA_DESPERDICIO : gera
    SETOR {
        NUMBER ID_SETOR PK
        VARCHAR2 NOME_SETOR
        NUMBER LIMITE_KWH_MENSAL
    }
    EQUIPAMENTO_IOT {
        NUMBER ID_EQUIPAMENTO PK
        NUMBER ID_SETOR FK
        VARCHAR2 NOME_EQUIPAMENTO
        VARCHAR2 TIPO
        VARCHAR2 STATUS_ENERGIA
    }
    LEITURA_CONSUMO {
        NUMBER ID_LEITURA PK
        NUMBER ID_EQUIPAMENTO FK
        DATE DATA_HORA_LEITURA
        NUMBER KWH_REGISTRADO
    }
    ALERTA_DESPERDICIO {
        NUMBER ID_ALERTA PK
        NUMBER ID_EQUIPAMENTO FK
        DATE DATA_ALERTA
        VARCHAR2 MENSAGEM
        VARCHAR2 NIVEL_CRITICIDADE
    }
```

## ⚙️ Automações (triggers)

| Trigger | Evento | Regra | Alerta |
|---|---|---|---|
| `TRG_ALERTA_PICO_CONSUMO` | Nova leitura | Consumo acima de **50 kWh** em uma única leitura | 🔴 CRÍTICO |
| `TRG_ALERTA_META_SETOR` | Nova leitura | Leitura maior que **20% do limite mensal** do setor | 🟠 ALTO |
| `TRG_STATUS_FALHA` | Mudança de status | Equipamento passa para `FALHA` ou `DEFEITO` | 🔴 CRÍTICO |
| `TRG_AUDITORIA_META` | Mudança de meta | Limite mensal do setor aumenta **mais de 20%** (governança) | 🟠 ALTO |

Os IDs dos alertas são gerados pela sequence `SEQ_ALERTA`.

## ▶️ Como executar

1. Abra o Oracle SQL Developer (ou outro cliente Oracle).
2. Execute o script [`automacao_esg.sql`](automacao_esg.sql) inteiro (F5): cria tabelas, carrega dados de teste e as triggers.
3. Teste as automações, por exemplo:

```sql
-- dispara pico de consumo (CRÍTICO) e risco à meta do setor (ALTO)
INSERT INTO LEITURA_CONSUMO VALUES (6, 1, SYSDATE, 1200);

-- dispara registro de falha
UPDATE EQUIPAMENTO_IOT SET STATUS_ENERGIA = 'FALHA' WHERE ID_EQUIPAMENTO = 2;

-- dispara auditoria de governança
UPDATE SETOR SET LIMITE_KWH_MENSAL = 2000 WHERE ID_SETOR = 3;

SELECT * FROM ALERTA_DESPERDICIO ORDER BY ID_ALERTA;
```

---

## 👤 Autor

**Christian Santos** — Estudante de Análise e Desenvolvimento de Sistemas na FIAP · Desenvolvedor Back-end & Mobile

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Christian_Santos-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/christian-santos-6530bb247/)
[![Portfólio](https://img.shields.io/badge/Portfólio-christiangit2025--sys.github.io-111827?logo=githubpages&logoColor=white)](https://christiangit2025-sys.github.io)
