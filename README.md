# EXEMPLO (SOLUÇÃO HIPOTÉTICA) — Projeto de Testes — Clipz-App
Disciplina: Teste de Software  
Professor: Edson Vaz Lopes

> **AVISO:** este documento é um **exemplo resolvido** para um aplicativo **hipotético** semelhante ao Shortz-App.  
> Use apenas como referência de **formato**, **clareza** e **nível de detalhe**.

---

## Resumo do Clipz-App (hipotético)
Plataforma de vídeos curtos (até 60s), com cadastro/login, upload, feed, curtidas e comentários.

### Regras (hipotéticas) usadas neste exemplo
- **R1:** Vídeo deve ter **até 60 segundos**.
- **R2:** Formatos aceitos: **MP4** e **WebM**.
- **R3:** Título do vídeo é obrigatório: **5 a 100 caracteres**.
- **R4:** Senha de cadastro: **mínimo 8 caracteres**.
- **R5:** Email deve ter **formato válido**.
- **R6:** Biografia do perfil: **até 255 caracteres**.
- **R7:** Username é **único**.
- **R8:** Uma curtida por usuário por vídeo (curtir/descurtir alterna estado).

---

## Seção 1 — Casos de Teste Funcionais (exemplo com 8)
**Observação:** estes testes viram depois testes de **integração** (API) e/ou **E2E** (UI).

| ID  | Cenário | Entrada | Resultado esperado |
|---|---|---|---|
| TC01 | Cadastrar usuário com dados válidos | username=`ana01`, email=`ana@ex.com`, senha=`Abc12345` | Conta criada; redireciona/login habilitado |
| TC02 | Cadastro falha por username duplicado | username=`ana01` já existente | Erro informando indisponibilidade do username |
| TC03 | Login válido | login=`ana@ex.com`, senha correta | Sessão iniciada; redireciona para feed |
| TC04 | Login falha por senha incorreta | login válido, senha errada | Mensagem “credenciais inválidas”; não cria sessão |
| TC05 | Upload de vídeo válido | video=mp4 45s, titulo=`Meu clip`, capa válida | Vídeo publicado e aparece em “Meus vídeos” |
| TC06 | Upload falha por duração acima do limite | video=mp4 61s | Erro “duração máxima 60s”; não publica |
| TC07 | Curtir e descurtir vídeo | usuário logado clica curtir e depois descurtir | Like incrementa; depois decrementa; estado alterna |
| TC08 | Comentar em vídeo | comentário=`Top!` (não vazio) | Comentário aparece na lista; contador atualiza |

---

## Seção 2 — Testes de Validação de Dados (exemplo com 10)
**Observação:** estes testes viram depois testes **unitários** (funções de validação).

| ID | Campo | Entrada | Resultado esperado |
|---|---|---|---|
| VAL01 | Senha | `abc123` (6 caracteres) | Erro: mínimo 8 |
| VAL02 | Senha | `Abc12345` (8 caracteres) | OK |
| VAL03 | Email | `ana.ex.com` (sem `@`) | Erro: formato inválido |
| VAL04 | Email | `ana@ex.com` | OK |
| VAL05 | Username | `a` (muito curto, regra interna) | Erro: fora do padrão |
| VAL06 | Username | `ana01` (válido) | OK |
| VAL07 | Título do vídeo | `Oi` (2 caracteres) | Erro: mínimo 5 |
| VAL08 | Título do vídeo | 101 caracteres | Erro: máximo 100 |
| VAL09 | Biografia | 256 caracteres | Erro: máximo 255 |
| VAL10 | Comentário | string vazia / só espaços | Erro: comentário obrigatório |

---

## Seção 3 — Testes de Regras de Negócio (exemplo com 10)
**Observação:** aqui você testa a **lógica do domínio** (regras do sistema).

| ID | Regra/Requisito | Entrada | Resultado esperado |
|---|---|---|---|
| RN01 | R1: duração máxima do vídeo é 60s | duração=60 | OK (aceita) |
| RN02 | R1: acima do limite deve falhar | duração=61 | Erro (rejeita) |
| RN03 | R2: formatos aceitos MP4/WebM | arquivo=.mp4 | OK |
| RN04 | R2: formato não aceito deve falhar | arquivo=.avi | Erro (rejeita) |
| RN05 | R3: título obrigatório | título vazio | Erro |
| RN06 | R3: título entre 5 e 100 | título=`Video massa` | OK |
| RN07 | R7: username único | username duplicado | Erro |
| RN08 | R8: 1 like por usuário por vídeo | usuário curte 2x o mesmo vídeo | 2º clique alterna para descurtir (não soma 2) |
| RN09 | Feed “Seguindo” prioriza seguidos | usuário segue A; A tem vídeos | Feed mostra vídeos de A antes do global |
| RN10 | Comentário associado a user+vídeo | user=ana01, video=V10, texto=`ok` | Comentário aparece com autor correto |

---

## Seção 4 — Requisitos Testáveis (exemplo com 12)
- RT01 — Senha deve ter no mínimo 8 caracteres.
- RT02 — Email deve ter formato válido.
- RT03 — Username deve ser único.
- RT04 — Vídeo deve ter duração máxima de 60 segundos.
- RT05 — Upload aceita apenas MP4 e WebM.
- RT06 — Título do vídeo é obrigatório e tem 5 a 100 caracteres.
- RT07 — Biografia do perfil tem no máximo 255 caracteres.
- RT08 — Comentário não pode ser vazio.
- RT09 — Like alterna estado curtir/descurtir (não permite duplicar curtida).
- RT10 — Feed “Seguindo” prioriza vídeos de perfis seguidos.
- RT11 — Busca parcial encontra termos não exatos.
- RT12 — Playlist não pode ter o mesmo vídeo duplicado (se existir esse recurso).

---

## Como isso vira teste automatizado depois (exemplo)
**Mapeamento sugerido:**
- **Unitário (Vitest/Jest):** VAL01–VAL10, RN01–RN08 (funções puras de validação e regras).
- **Integração (Supertest):** TC01–TC08 (rotas de auth/upload/like/comment).
- **E2E (Cypress/Playwright):** TC03, TC05, TC07 (fluxos completos na interface).
