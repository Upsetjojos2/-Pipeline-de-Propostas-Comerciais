# 🚀 Pipeline de Propostas Comerciais — Salesforce CRM

Projeto de estudo e portfólio desenvolvido do zero em uma org **Salesforce Developer Edition**, simulando um sistema real de gestão de propostas comerciais para uma empresa fictícia (TechBR Soluções).

O projeto cobre as duas principais trilhas de carreira Salesforce: **Admin** e **Developer**.

---

## 📋 Visão Geral

O objetivo foi construir um CRM funcional de pipeline de vendas, com modelagem de dados customizada, automações, código Apex com testes unitários e dashboard executivo — tudo sem usar pacotes externos.

---

## 🏗️ Arquitetura do Projeto

### Admin Side

#### Objeto Customizado — `Proposta__c`
Objeto criado do zero para representar propostas comerciais da empresa.

| Campo | API Name | Tipo | Observação |
|---|---|---|---|
| Nome da Proposta | `Name` | Texto(80) | Campo padrão |
| Valor da Proposta | `Valor_da_Proposta__c` | Moeda(16,2) | Obrigatório |
| Status da Proposta | `Status_da_Proposta__c` | Picklist | 5 estágios, padrão: Rascunho |
| Data de Fechamento | `Data_de_Fechamento__c` | Data | Obrigatório |

**Opções ativadas:** Relatórios, Atividades, Rastrear histórico de campos.

#### Validation Rule — `Exigir_Data_para_Proposta_Fechada`
Bloqueia salvar uma proposta com status "Fechada" sem data de fechamento preenchida.

```
AND(
  ISPICKVAL(Status_da_Proposta__c, "Fechada"),
  ISBLANK(Data_de_Fechamento__c)
)
```

**Mensagem de erro:** `Informe a Data de Fechamento antes de marcar a proposta como Fechada.`

#### Flow — `Notificar Criacao de Proposta`
- **Tipo:** Fluxo acionado por registro
- **Objeto:** `Proposta__c`
- **Acionador:** Um registro é criado
- **Ação:** Define automaticamente o status inicial como "Rascunho"

---

### Developer Side

#### Trigger Apex — `PropostaTrigger`
Roda em dois contextos:

**`before insert`** — Valida que o valor da proposta é maior que zero:
```apex
if (proposta.Valor_da_Proposta__c != null && 
    proposta.Valor_da_Proposta__c <= 0) {
    proposta.Valor_da_Proposta__c.addError(
        'O valor da proposta deve ser maior que zero.'
    );
}
```

**`before update`** — Bloqueia reabrir uma proposta fechada:
```apex
Proposta__c antiga = Trigger.oldMap.get(proposta.Id);
if (antiga.Status_da_Proposta__c == 'Fechada' &&
    proposta.Status_da_Proposta__c != 'Fechada') {
    proposta.Status_da_Proposta__c.addError(
        'Não é possível reabrir uma proposta fechada.'
    );
}
```

#### Classe de Teste — `PropostaTriggerTest`
| Método | O que testa |
|---|---|
| `testValorPositivo` | Garante que valor negativo é bloqueado pelo trigger |
| `testInsertValido` | Garante que proposta válida é inserida com sucesso |

**Resultado:** ✅ 0 falhas — 75% de cobertura de código

---

### Analytics

#### Relatório — `Pipeline de Propostas por Status`
- Tipo: Propostas
- Agrupado por: Valor da Proposta, Status, Data de Fechamento
- Gráfico: Rosca por valor

#### Dashboard — `Dashboard Pipeline de Propostas`
3 widgets gerados a partir do relatório:
- 🍩 Gráfico de rosca — distribuição por valor
- 🔢 Métrica — total de propostas ativas
- 🔻 Gráfico de funil — estágios do pipeline

---

## 🛠️ Tecnologias Utilizadas

- Salesforce Developer Edition (gratuita)
- Apex (Trigger + Test Class)
- Flow Builder (Record-Triggered Flow)
- Salesforce Reports & Dashboards
- Developer Console

---

## 📚 Conceitos Demonstrados

- Modelagem de dados com objetos e campos customizados
- Governança de dados com Validation Rules e Picklists
- Automação declarativa com Flow Builder
- Programação Apex com padrão bulkified (`Trigger.new`, `Trigger.oldMap`)
- Testes unitários com `@isTest`, `Test.startTest()` e `System.assert()`
- Visualização de dados com Reports & Dashboards

---

## 👨‍💻 Autor

**Gabriel Pereira**  
Salesforce Admin & Developer | Em desenvolvimento de carreira  
[LinkedIn](https://linkedin.com/in/seu-perfil)

---

## 📄 Licença

Este projeto foi desenvolvido para fins educacionais e de portfólio.
