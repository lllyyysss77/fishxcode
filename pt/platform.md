---
title: Navegação da plataforma e guia do sistema
description: Arquitetura FishXCode, funcionalidades do console, rotas e referência documental.
---

# Navegação da plataforma e guia do sistema

> Esta página apresenta a estrutura geral da plataforma FishXCode para ajudá-lo a localizar rapidamente a funcionalidade desejada.
> Precisa criar uma conta? Consulte [Configuração de conta e gestão](/pt/account).

## 1. Visão geral

O FishXCode é uma plataforma de acesso a IA voltada para desenvolvedores e equipes. Pense nele como um ponto de entrada unificado: na parte pública estão o site, a página de preços e a documentação; na parte interna estão o console, os tokens, a recarga, as faturas e os registros de uso.

A plataforma oferece serviços de intermediação para os principais modelos de IA. Os canais e modelos disponíveis estão sujeitos a alterações — consulte a [página de Preços](https://fishxcode.com/pricing) para informações atualizadas. Desde o registro até a criação de tokens, escolha de planos, integração com ferramentas e acompanhamento de consumo, todo o fluxo é realizado dentro do mesmo sistema.

---

## 2. Módulos do site

O sistema é composto por 4 módulos principais:

| Rota | Página | Módulo | Descrição |
| --- | --- | --- | --- |
| `/` | Página inicial | Site público | Apresenta as capacidades da plataforma, exibe anúncios e direciona para registro ou documentação |
| `/pricing` | Preços | Site público | Consulta preços dos modelos, planos e taxas de consumo por grupo |
| `/login` | Login | Central de autenticação | Login por nome de usuário/e-mail, com suporte a GitHub e LinuxDO |
| `/register` | Registro | Central de autenticação | Ponto de entrada para novos usuários |
| `/console` | Dashboard | Console | Visão geral: saldo, estatísticas de consumo, número de requisições e notificações do sistema |
| `/console/package` | Gestão de planos | Console | Visualizar e adquirir planos de assinatura, verificar modelos cobertos |
| `/console/token` | Gestão de tokens | Console | Criar, copiar e excluir API Keys, gerenciar grupos e validade |
| `/console/log` | Registro de uso | Console | Consultar cada chamada: horário, modelo, consumo e detalhes |
| `/console/topup` | Recarga | Console | Recarregar saldo, utilizar códigos de resgate, consultar histórico de recargas |
| `/console/invoice` | Gestão de faturas | Console | Solicitar emissão de fatura e acompanhar o andamento |
| `/console/invite` | Programa de indicação | Console | Link de convite, regras de recompensa, ganhos e ranking |
| `/console/personal` | Configurações pessoais | Console | Informações da conta, grupo padrão, recompensas de check-in |
| `/status` | Status do serviço | Página pública | Verificar se os serviços de cada grupo estão funcionando normalmente |
| `/contact` | Fale conosco | Página pública | Canais de contato oficial e suporte pós-venda |
| `/docs` | Central de documentação | Página pública | Tutoriais de integração, guias de ferramentas e FAQ |

---

## 3. Referência rápida do console

Após o login, todas as operações são realizadas no console. Localize rapidamente o que precisa por cenário de uso:

### Ativação do serviço

1. Registrar conta → fazer login no console
2. **Gestão de tokens**: criar uma API Key (selecionar o grupo adequado)
3. Configurar a Base URL e a API Key na sua ferramenta

### Compras e recarga

- **Gestão de planos**: visualizar planos disponíveis e assinar o mais adequado
- **Página de recarga**: suporta Alipay, WeChat e códigos de resgate
- **Consulta de saldo**: exibido em tempo real na página inicial do Dashboard

### Gerenciamento de uso

- **Registro de uso**: consultar o histórico de chamadas, incluindo modelo, consumo, IP e detalhes
- **Gestão de tokens**: verificar status dos tokens, escopo do grupo e excluir tokens não utilizados
- **Configurações pessoais**: alterar grupo padrão, verificar recompensas de check-in e estatísticas básicas

### Suporte e pós-venda

- **Gestão de faturas**: usuários corporativos ou com necessidade de reembolso podem solicitar emissão de fatura
- **Programa de indicação**: convidar novos usuários por link e acompanhar os ganhos de recompensa
- **Status do serviço**: verificar se os grupos estão operando normalmente ao diagnosticar falhas de chamada
- **Fale conosco**: resolver problemas de uso, negociações comerciais e suporte pós-venda

---

## 4. Referência documental

`/docs` é a central de documentação do FishXCode. O conteúdo corresponde diretamente às páginas deste site de documentação:

| Página de documentação | Rota | Conteúdo |
| --- | --- | --- |
| Página inicial | `/` | Página inicial da documentação e visão geral do produto |
| Começar | `/start` | Tutorial de integração do Claude Code |
| Conta | `/account` | Registro, login, tokens, recarga e gestão de conta |
| ZCF | `/zcf` | Fluxo de trabalho ZCF e configuração MCP |
| OpenAI Codex | `/codex` | Guia de integração do Codex |
| Cursor | `/cursor` | Guia de integração do Cursor |
| RooCode | `/roocode` | Guia de integração do RooCode |
| Qwen Code | `/qwencode` | Guia de integração do Qwen Code |
| Droid CLI | `/droid` | Guia de integração do Droid CLI |
| OpenCode | `/opencode` | Guia de integração do OpenCode |
| OpenClaw | `/openclaw` | Guia de integração do OpenClaw |
| Comparação de ferramentas | `/compare` | Comparação entre múltiplas ferramentas |
| Modelos suportados | `/models` | Lista de modelos e escopo de capacidades |
| FAQ | `/faq` | Perguntas frequentes e solução de problemas |
| Changelog | `/changelog` | Registro de atualizações de versão |
| Termos de uso | `/terms` | Termos da plataforma |
| Política de privacidade | `/privacy` | Declaração de privacidade |

::: tip Em resumo
O usuário conhece a plataforma pelo site público, ativa o serviço pelo registro e console, e depois utiliza as páginas de tokens, planos, recarga e registros para o uso efetivo. A documentação e a página de status fornecem orientação e garantia.
:::
