# Introdução

**Teste Caixa Cinza com Supabase e Postman**

- **Aluno:** Lucas Senger Medeiros
- **Curso:** Análise e Desenvolvimento de Sistemas (ADS) – Facens
- **Componente curricular:** UX/UI e Testes de Software (AS406TSN1) – Aula 11
- **Data de execução dos testes:** 07/11/2026

Este repositório documenta a atividade de testes funcionais de caixa cinza em uma API de autenticação. Criei um ambiente de autenticação por e-mail e senha no **Supabase**, cadastrei um usuário de teste e executei cinco cenários de login com o **Postman Desktop**, validando status HTTP, corpo JSON, mensagens de erro e o retorno do *access token*. Não foi desenvolvida interface gráfica: todo o trabalho foi feito diretamente sobre a API.

Estrutura do repositório:

```
/README.md
/postman/    -> coleção e environment exportados do Postman (sem chave e sem senha)
/prints/     -> evidências reais geradas durante a atividade
/planilha/   -> planilha de registro dos testes
```

---

# Objetivo da Atividade

Aplicar testes de caixa cinza em uma API de autenticação, usando ferramentas de mercado para configurar, executar e validar testes funcionais. Na atividade:

- configurei um ambiente de autenticação no Supabase;
- criei um usuário válido para os testes;
- compreendi o funcionamento de requisições HTTP (método, endpoint, headers e body);
- usei o Postman para executar os testes;
- validei as respostas de autenticação;
- documentei todo o processo neste README.

No teste de caixa cinza, o testador conhece parcialmente a estrutura do sistema. Aqui eu conhecia o endpoint, a forma de autenticação, os headers, o formato do body JSON e as respostas HTTP, mas não o código interno do Supabase.

---

# Configuração do Supabase

**Como o projeto foi criado.** Acessei o site do Supabase, entrei com minha conta e criei um novo projeto chamado `caixacinza-lucas-senger` (plano Free, região `us-east-1`, com as opções de segurança e configuração avançada no padrão). O nome do projeto contém meu nome para identificar o ambiente como meu.

**Tipo de autenticação.** Utilizei **autenticação por e-mail e senha** (provider *Email*). No menu **Authentication → Sign In / Providers**:

- confirmei que o provider **Email** está **Enabled** (ele estava desativado e eu o habilitei);
- desativei a opção **Confirm email**, para que o usuário de teste consiga entrar sem precisar confirmar o e-mail.

**Como o usuário foi criado.** Em **Authentication → Users**, usei o botão **Add user** e cadastrei manualmente um usuário válido (e-mail e senha), que foi usado em todos os testes.

**Credenciais da API.** Em **Project Settings → API Keys** obtive a **Publishable key** (usada como `api_key`) e, na tela inicial do projeto, a **Project URL** (usada como `base_url`). A *Secret key* não foi usada nem exposta. Neste README e nos arquivos exportados, a chave e a senha aparecem como `XXXXX`.

**Objetivo da configuração.** Ter um serviço real de autenticação, com banco de usuários e API pronta, para validar o login por meio de requisições HTTP.

**Evidências**

| Evidência | Print |
|---|---|
| Lista de projetos (projeto criado) | ![Lista de projetos](prints/01-supabase-lista-projetos.png) |
| Painel do projeto (Project Overview) | ![Painel do projeto](prints/02-supabase-project-overview.png) |
| Autenticação por e-mail habilitada e Confirm email desligado | ![Sign In / Providers](prints/03-supabase-signin-providers.png) |
| Usuário criado | ![Usuário criado](prints/04-supabase-usuario-criado.png) |
| Credenciais da API (chave tarjada) | ![Credenciais da API](prints/05-supabase-api-keys.png) |

A identificação do aluno aparece no nome do projeto (`caixacinza-lucas-senger`) e no avatar da conta, no canto superior de cada print.

---

# Configuração do Postman

**Workspace.** Instalei o Postman Desktop, fiz login e criei um workspace em branco chamado **Caixa Cinza Supabase**, do tipo *Internal*, para reunir todos os testes da atividade.

**Environment.** Criei o environment **Supabase Auth** com as quatro variáveis abaixo e o selecionei como ambiente ativo:

| Variável | Descrição |
|---|---|
| `base_url` | URL do projeto Supabase (API) |
| `api_key` | Publishable key do projeto (marcada como *secret*) |
| `email` | E-mail do usuário de teste criado |
| `password` | Senha do usuário de teste (marcada como *secret*) |

**Finalidade das variáveis.** Evitam repetir dados em cada requisição e mantêm credenciais fora do corpo das chamadas: as requisições usam `{{base_url}}`, `{{api_key}}`, `{{email}}` e `{{password}}`. Se a URL ou a chave mudar, basta alterar o environment. Marcar `api_key` e `password` como *secret* esconde os valores nos prints.

**Como o Postman foi usado nos testes.** Criei a coleção **Supabase Auth Tests** com uma requisição por cenário. Cada uma foi executada com **Send** e as respostas (status, tempo, JSON) foram analisadas e registradas.

**Evidências**

| Evidência | Print |
|---|---|
| Workspace criado | ![Workspace](prints/06-postman-workspace.png) |
| Environment com as variáveis (valores sensíveis ocultos) | ![Environment](prints/07-postman-environment.png) |
| Organização do Postman (environment selecionado) | ![Organização do Postman](prints/08-postman-organizacao.png) |

---

# Configuração das Requisições

**Endpoint utilizado**

```
{{base_url}}/auth/v1/token?grant_type=password
```

**Método HTTP:** `POST`

**Headers utilizados**

| Header | Valor |
|---|---|
| `apikey` | `XXXXX` (valor da variável `{{api_key}}`) |
| `Authorization` | `Bearer XXXXX` (valor da variável `{{api_key}}`) |
| `Content-Type` | `application/json` |

**Body JSON utilizado** (formato *raw → JSON*)

```json
{
  "email": "usuario@email.com",
  "password": "123456"
}
```

No Postman, o body usa as variáveis do environment:

```json
{
  "email": "{{email}}",
  "password": "{{password}}"
}
```

**Finalidade da requisição.** Realizar o login por e-mail e senha na API de autenticação do Supabase (fluxo *password grant*). Com credenciais válidas, a API devolve o *access token*; com credenciais inválidas, devolve um erro. As outras quatro requisições da coleção reutilizam a mesma configuração e alteram apenas o body.

**Evidências**

| Evidência | Print |
|---|---|
| Método e endpoint configurados | ![Endpoint](prints/09-postman-endpoint.png) |
| Headers | ![Headers](prints/10-postman-headers.png) |
| Body JSON | ![Body](prints/11-postman-body.png) |
| Coleção com as 5 requisições criadas | ![Coleção](prints/12-postman-colecao.png) |

---

# Execução dos Testes

Executei os cinco cenários obrigatórios, um por requisição, com o environment *Supabase Auth* ativo. Em cada um validei o status HTTP, o JSON da resposta, a mensagem de erro (quando houve) e a presença ou ausência do *access token*.

**Tabela de Cenários**

| Cenário | Entrada Utilizada | Resultado Esperado | Resultado Obtido | Status |
|---|---|---|---|---|
| Login válido | E-mail e senha corretos do usuário de teste | Status 200 + access token | 200 OK, retornou `access_token`, `token_type: bearer`, `expires_in: 3600` e `refresh_token` | OK |
| Senha incorreta | E-mail correto e senha `senhaErrada999` | Erro de autenticação | 400 Bad Request, `invalid_credentials`, "Invalid login credentials" | OK |
| Usuário inexistente | E-mail `naoexiste@teste.com` e senha `123456` | Acesso negado | 400 Bad Request, `invalid_credentials`, "Invalid login credentials" | OK |
| Campos vazios | E-mail e senha em branco | Erro de validação | 400 Bad Request, `validation_failed`, "missing email or phone" | OK |
| Credenciais inválidas | E-mail `abc` (formato inválido) e senha `1` | Mensagem de erro | 400 Bad Request, `invalid_credentials`, "Invalid login credentials" | OK |

**Erros encontrados durante a execução**

Durante a montagem, o primeiro envio da requisição de login válido retornou **401 Unauthorized** com a mensagem `No API key found in request`. A tabela de Headers estava vazia (apenas os headers automáticos do Postman) e o Body não estava preenchido. Ao ler a resposta do servidor, entendi que o header `apikey` não estava sendo enviado. Adicionei os três headers, preenchi o body em JSON, salvei e reenviei, e o login passou a retornar 200.

![Erro 401 sem apikey](prints/dificuldade-01-sem-apikey-401.png)

**Evidências dos testes** (tokens tarjados nos prints)

| Teste | Print |
|---|---|
| 1. Login válido (200 + access token) | ![Teste 1](prints/13-teste-1-login-valido.png) |
| 2. Senha incorreta | ![Teste 2](prints/14-teste-2-senha-incorreta.png) |
| 3. Usuário inexistente | ![Teste 3](prints/15-teste-3-usuario-inexistente.png) |
| 4. Campos vazios | ![Teste 4](prints/16-teste-4-campos-vazios.png) |
| 5. Credenciais inválidas | ![Teste 5](prints/17-teste-5-credenciais-invalidas.png) |

---

# Registro dos Testes

**Como os testes foram registrados.** Registrei cada execução na planilha [`planilha/planilha_testes.xlsx`](planilha/planilha_testes.xlsx), com os campos: ID do teste, cenário, entrada utilizada, resultado esperado, resultado obtido, status (Sucesso/Falha) e observações. O resultado obtido foi copiado do que o Postman devolveu (status, `error_code` e `msg`), e não do que eu esperava.

**Importância da documentação dos testes.** O registro permite repetir os mesmos testes depois, comparar resultados quando a API mudar, comprovar o que foi feito e localizar falhas com rapidez. Sem ele, o teste vira uma verificação informal e não deixa evidência.

**Falhas identificadas.** Nenhum cenário teve comportamento diferente do esperado, então todos foram marcados como *Sucesso*. Registrei como observações:

- senha incorreta, usuário inexistente e credenciais inválidas retornam **a mesma mensagem** (`Invalid login credentials`), o que impede descobrir se um e-mail existe;
- o login com credenciais inválidas retorna **400**, e não 401, como eu imaginava inicialmente;
- o e-mail `abc` (formato inválido) não gerou erro de formato no login, apenas credenciais inválidas.

| Evidência | Print |
|---|---|
| Planilha preenchida | ![Planilha preenchida](prints/18-planilha-preenchida.png) |
| Exemplos de registros | ![Exemplos de registros](prints/19-planilha-exemplos.png) |

---

# Resultados Obtidos

| # | Cenário | Status HTTP | `error_code` | Mensagem | Token retornado | Tempo |
|---|---|---|---|---|---|---|
| 1 | Login válido | 200 OK | – | – | Sim | 1,06 s |
| 2 | Senha incorreta | 400 Bad Request | `invalid_credentials` | Invalid login credentials | Não | 881 ms |
| 3 | Usuário inexistente | 400 Bad Request | `invalid_credentials` | Invalid login credentials | Não | 547 ms |
| 4 | Campos vazios | 400 Bad Request | `validation_failed` | missing email or phone | Não | 502 ms |
| 5 | Credenciais inválidas | 400 Bad Request | `invalid_credentials` | Invalid login credentials | Não | 511 ms |

**Resumo:** 5 de 5 cenários executados e com o comportamento esperado. Apenas o login válido gerou *access token*. Os quatro cenários inválidos foram recusados pela API, três deles na etapa de autenticação (`invalid_credentials`) e um na validação dos campos (`validation_failed`).

---

# Conclusão

**Os testes foram executados corretamente?** Sim. Os cinco cenários obrigatórios foram executados por mim no Postman e todos os resultados estão comprovados por prints e registrados na planilha.

**A autenticação funcionou?** Sim. O login válido retornou 200 com o *access token* (válido por 3600 segundos), e todos os cenários inválidos foram recusados sem emitir token.

**Dificuldades encontradas.** A principal foi o erro 401 no primeiro envio, causado por headers e body que não tinham sido preenchidos no Postman. A própria mensagem da API (`No API key found in request`) mostrou onde estava o problema. Outra foi confirmar que o provider *Email* estava habilitado no Supabase, pois ele aparecia desativado. Também precisei renomear e salvar as requisições com cuidado, para que os prints identificassem o teste certo, e ocultar chave, senha e tokens antes de publicar as evidências.

**Falhas identificadas.** Nenhuma falha funcional. As observações foram: a mensagem de erro idêntica para senha errada, usuário inexistente e credenciais inválidas; o status 400 (e não 401) nas recusas de login; e a ausência de erro específico para e-mail com formato inválido no login.

**Melhorias que poderiam ser implementadas.**

- adicionar *scripts* de teste no Postman (`pm.test`) para validar status e mensagens automaticamente;
- incluir mais cenários: e-mail com letras maiúsculas, senha muito longa, espaços em branco, caracteres especiais e tentativa de injeção;
- testar o limite de tentativas de login (*rate limit*) e a expiração do token;
- executar a coleção por linha de comando com o Newman;
- habilitar a confirmação de e-mail e a autenticação em dois fatores no ambiente final;
- usar environments separados para desenvolvimento e produção.

**Importância dos testes de caixa cinza em APIs.** Eles combinam conhecimento parcial da estrutura (endpoint, headers, body, formato das respostas) com a verificação de entradas e saídas. Assim é possível testar a API sem depender de uma interface, encontrar erros de configuração (como o header ausente) e confirmar mensagens e códigos HTTP. Também verificam se a autenticação protege o sistema, recusando acessos indevidos sem expor informações sensíveis.
