---
title: Configuração de conta e gestão
description: Registro no FishXCode, obtenção de API Key, recarga e segurança da conta.
---

# Configuração de conta e gestão

> Já tem uma conta? Vá direto para [Começar](/pt/start) para configurar suas ferramentas.
> Explorar a plataforma completa? Veja o [Guia da plataforma](/pt/platform).

## 1. Obter uma API Key

### 1. Criar conta

Acesse [fishxcode.com](https://fishxcode.com/register?aff=9CTW) e clique em **Registrar**:

![Página inicial do FishXCode](/img/start/api-01-home.png)

Escolha o método de registro (GitHub, LinuxDO ou nome de usuário):

![Opções de registro](/img/start/api-02-register.png)

Preencha nome de usuário, senha e confirmação de senha:

![Formulário de registro](/img/start/api-03-register-form.png)

### 2. Fazer login

Após o registro, faça login com nome de usuário e senha:

![Página de login](/img/start/api-04-login.png)

Após o login, acesse o console:

![Console principal](/img/start/api-05-console.png)

### 3. Criar token

Vá em **Console → Gerenciamento de tokens → Adicionar token** e preencha o formulário:

![Adicionar token](/img/start/api-06-token-create.png)

- Em **Grupo de tokens**, selecione o **canal oficial**. Este grupo inclui toda a linha de modelos Claude e seleciona automaticamente o modelo com base na complexidade da tarefa. Os canais e modelos disponíveis estão sujeitos a alterações — consulte a [página de Preços](https://fishxcode.com/pricing) para informações atualizadas.

Após a criação, clique em **Copiar** na lista de tokens para obter sua API Key (formato: `sk-xxx`):

![Copiar token](/img/start/api-07-token-copy.png)

### 4. Recarregar saldo

Vá em **Console → Carteira**, suporta Alipay, WeChat Pay ou código de resgate:

![Página de recarga](/img/start/api-08-wallet.png)

| Método | Caminho |
|---|---|
| Alipay | Inserir/selecionar valor → Alipay |
| WeChat Pay | Inserir/selecionar valor → WeChat |
| Código de resgate | Inserir código → Resgatar créditos |

::: tip Multiplicador de taxa
Um grupo rotulado como `0.5x` significa que você obtém o dobro de tokens em relação ao preço oficial. Exemplo: `0.5x` = 10M tokens por ¥1 (vs. 5M no preço oficial).
:::

---

## 2. Gerenciamento de tokens

### Detalhes dos grupos de tokens

Ao criar um token, é necessário escolher um grupo. Cada grupo determina os modelos disponíveis e a taxa de consumo:

| Tipo de grupo | Descrição | Cenário de uso |
|---|---|---|
| **Canal oficial** | Inclui toda a linha de modelos Claude, roteamento automático | Recomendado para a maioria dos usuários |
| **Grupos de taxa reduzida** | Taxa de consumo mais baixa, melhor custo-benefício | Chamadas frequentes, cenários sensíveis a custos |

::: tip Recomendação
Se você não tem certeza de qual grupo escolher, selecione o **Canal oficial**. O sistema escolherá automaticamente o modelo mais adequado com base na complexidade da tarefa.
:::

### Boas práticas de segurança de tokens

- **Rotação periódica**: recomenda-se trocar os tokens a cada 30–90 dias para reduzir o risco de vazamento
- **Não armazenar em texto puro**: evite escrever a API Key diretamente no código ou fazer commit em repositórios Git — utilize variáveis de ambiente
- **Um token por projeto**: crie tokens independentes para cada projeto, facilitando o gerenciamento e o rastreamento
- **Em caso de vazamento**: se suspeitar que um token foi exposto, exclua-o imediatamente no console e crie um novo

### Modificar e excluir tokens

Na página **Console → Gerenciamento de tokens**:

- **Ver detalhes**: clique no nome do token para visualizar grupo, escopo de modelos e data de criação
- **Copiar token**: clique no botão de copiar na lista
- **Excluir token**: clique no botão de excluir — o token será invalidado imediatamente e as ferramentas que o utilizam deixarão de funcionar

::: warning Atenção
A exclusão é irreversível. Confirme que o token não está mais em uso antes de excluí-lo.
:::

---

## 3. Segurança da conta

### Senha e segurança de login

- Utilize uma **senha forte** (mínimo de 8 caracteres, incluindo letras maiúsculas, minúsculas e números)
- Não reutilize a mesma senha em diferentes plataformas
- Se esquecer a senha, utilize o fluxo de recuperação na página de login para redefini-la

### Login com terceiros

O FishXCode suporta os seguintes métodos de login com terceiros:

| Método | Descrição |
|---|---|
| **GitHub** | Recomendado para desenvolvedores, login com autorização em um clique |
| **LinuxDO** | Usuários da comunidade podem fazer login diretamente |

Após vincular uma conta de terceiros, você pode usar a autorização da plataforma correspondente para fazer login sem digitar a senha.

### Detecção de anomalias e resolução

Se você identificar alguma das seguintes situações anormais, tome providências imediatamente:

1. **Aumento anormal no consumo** → verifique se algum token foi vazado; se necessário, exclua todos os tokens e crie novos
2. **Não consegue fazer login** → tente recuperar a senha; se o problema persistir, entre em contato com o suporte oficial
3. **Dedução anormal de saldo** → consulte os registros de uso para verificar as chamadas específicas e confirmar se o consumo é legítimo

::: tip Contato com o suporte
Em caso de problemas com a conta, acesse a página [Fale conosco](https://fishxcode.com/contact) para obter ajuda.
:::

---

## 4. Perguntas frequentes

### Não recebe o código de verificação?

1. Verifique se o e-mail foi digitado corretamente (atenção à ortografia e ao domínio)
2. Confira a pasta de spam/lixo eletrônico
3. Aguarde 1–2 minutos e tente novamente — evite solicitações frequentes
4. Se ainda não receber, tente outro endereço de e-mail ou utilize o login com terceiros

### Onde encontrar os tokens criados?

Acesse **Console → Gerenciamento de tokens**. Os tokens criados são exibidos na lista. Clique no botão de copiar para obter a API Key no formato `sk-xxx`.

### Recarga não refletida?

1. Confirme se o pagamento foi concluído (verifique o histórico de transações no Alipay/WeChat)
2. Aguarde de 1 a 5 minutos — pode haver um breve atraso no sistema
3. Atualize a página da carteira no console
4. Se após 10 minutos o saldo ainda não for atualizado, entre em contato com o suporte e forneça o comprovante de pagamento

### Como verificar detalhes de consumo?

Acesse **Console → Registro de uso** para visualizar os detalhes de cada chamada de API:
- Data/hora e nome do modelo
- Quantidade de tokens consumidos e custo
- IP da requisição e registros detalhados
