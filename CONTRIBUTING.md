# Como trabalhar no repositório do grupo

Este documento define o fluxo de trabalho obrigatório da disciplina. Leia antes do primeiro commit.

## Fluxo por entrega

1. Um integrante cria a branch da etapa a partir da `main` atualizada:
   ```bash
   git checkout main && git pull
   git checkout -b entrega-e1    # depois: entrega-e2, entrega-e3, entrega-e4
   ```
2. Desenvolvam na branch, com commits pequenos e mensagens descritivas
   (`git commit -m "Adiciona seção de artigo-base no README"`).
3. Ao concluir, abram um **Pull Request** de `entrega-eX` para `main`:
   - Na página do PR, em "Description", escolha o template da etapa (E1, E2, E3 ou E4).
   - O link direto já pode vir com o template: `https://github.com/<org>/<repo>/compare/main...entrega-e1?template=E1.md`
   - Preencham o checklist da etapa.
4. O professor revisa, comenta e aprova. **Somente ele mescla o PR.**
5. Após o merge, criem a tag da etapa (opcional, recomendado):
   ```bash
   git checkout main && git pull
   git tag e1 && git push origin e1
   ```

## Regras

- **Commits:** histórico claro e organizado é critério de avaliação. Prefiram vários commits
  pequenos a um único commit gigante na véspera da entrega.
- **Branch `main`:** protegida. Nada entra sem PR aprovado pelo professor (Code Owner),
  e commits diretos na `main` são bloqueados — a proteção vem de um ruleset da organização,
  configurado uma única vez pelo professor.
- **Dados e pesos:** arquivos acima de 50 MB não podem entrar no repositório (a CI bloqueia).
  Usem link externo (Google Drive, Hugging Face) e instruções no README.
- **Segredos:** credenciais e chaves de API **nunca** são versionadas. Usem variáveis de
  ambiente (`.env`, que está no `.gitignore`) e documentem no README quais variáveis são
  necessárias.
- **Uso de código existente:** é permitido usar implementações oficiais e bibliotecas,
  com créditos, licenças e descrição clara das contribuições do grupo no README.
- **Estrutura:** mantenham a estrutura de diretórios legível:
  ```
  src/        # código-fonte (treino, inferência, aplicação)
  docs/       # decisoes.md, experimentos.md, relatorio.md
  data/       # apenas arquivos pequenos/exemplo; o resto via link externo
  notebooks/  # (opcional) análise exploratória
  ```
