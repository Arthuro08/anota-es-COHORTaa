# 📊 Projeto: Cohort de Faturamento (Ativação e Retenção)

Este documento centraliza as regras de negócio, origens de dados e definições pendentes para a construção da tabela de Cohort de Faturamento da companhia.

## 1. Regras de Negócio e Fonte de Dados

*   **Fonte da Verdade:** Os dados de faturamento devem ser extraídos obrigatoriamente do **SAP HANA**. 
    *   *Por que não o Salesforce?* O CRM reflete a expectativa de venda, não a realidade financeira consolidada.
*   **Fato Gerador (Métrica de Faturamento):** Utilizar a **Emissão de Nota Fiscal**.
    *   *Atenção:* **NÃO** utilizar a geração de boletos. Um boleto gerado não implica que o cliente de fato comprou ou pagou pelo produto/serviço.
*   **Definição do "Mês 0":** O Mês 0 corresponde ao momento em que o usuário **cria a conta e é registrado no banco de dados**. 
    *   Isso transforma o nosso cohort em uma tabela de ativação e retenção, medindo quanto tempo um usuário leva para transacionar após entrar na base.
*   **Tratamento de Meses Futuros/Vazios:** Para os meses em que o cliente ainda não possuía tempo de vida no sistema (o "futuro" relativo à data de entrada), a matriz deve ser limpa para não distorcer as médias. A recomendação técnica no processamento é utilizar espaços vazios (ex: `fillna("")` no Pandas) ao exportar a visualização final em formato de escada.

---

## 2. Sugestão de Arquitetura para depois: Separação por Grupo Econômico (GE)

Para enriquecer a análise, propõe-se agrupar os clientes por Grupo Econômico (GE), separando as contas de Pessoa Física (PF) e Pessoa Jurídica (PJ) em colunas paralelas para visualização do comportamento unificado.

**Exemplo de Estrutura Visual:**

| Grupo Econômico | PF | PJ |
| :--- | :--- | :--- |
| **GE_A** | F1 | J1 |
| **GE_B** | F2 | *Nulo* |
| **GE_C** | *Nulo* | J3 |

*   *Nota:* Valores nulos em uma das colunas significam que o Grupo Econômico não possui conta daquele tipo específico.
*   **Pré-requisito:** Essa visualização só será viável após a higienização e organização prévia dos dados de Grupos Econômicos no banco.

---

## 3. Próximos Passos e Ações (To-Do)

- [ ] **Alinhamento com o Vitor:** Conversar sobre o projeto antigo de Notas Fiscais que ele desenvolveu. O objetivo é entender se conseguimos reaproveitar a lógica de emissão de NF para extrair, além do valor da fatura, os produtos específicos que o cliente comprou.

---

## 4. Dúvidas em Aberto depois pq se trata de GE(Para Definição)

Antes de rodar a extração final, precisamos definir os seguintes pontos com a área de negócios/dados:

1.  **Tratamento PF/PJ:** Como o BI recebe essas informações atualmente? O Salesforce já envia os dados com a separação (PF vs PJ) e o mapeamento de Grupo Econômico de forma estruturada?
2.  **Janela Histórica (Corte de Dados):** A partir de qual mês devemos considerar o início da análise (Mês 0)? Vamos puxar toda a base histórica desde a fundação ou estabelecer um marco inicial (ex: iniciar a puxada a partir de Fevereiro de 2025)?
