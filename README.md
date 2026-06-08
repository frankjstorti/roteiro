# Diagnóstico do JOIN — Roteiro MENSAL × Catálogo de Verbas

## Insumos
- **Mensal.xlsx** (aba `Mensal`): 369 passos do roteiro de execução.
- **Verbas.xlsx** (aba `Verbas`): 2.333 verbas no catálogo mestre.

## Chave escolhida
**`Cod. Verba` (Mensal) ↔ `Código da Verba` (Verbas)**

É a única chave confiável:

- **318 de 369 passos casaram** — e dos passos que têm `Cod. Verba` preenchido, **100% casaram (0 órfãos)**.
- `Código da Verba` é **único no catálogo** (0 duplicatas).
- Conferência cruzada de nome: `Nome Verba` (Mensal) vs `Nome da Verba` (catálogo) nos 318 casados → **0 divergências**.

## Passos sem vínculo
**51 passos** (369 − 318) não têm `Cod. Verba` no roteiro. Não é falha de join: são passos
estruturais, não verbas — cálculos básicos (`CALCULO_BASICO`), lançamentos, contabilização,
provisões e bases (ex.: "Provisão Dissídio", "ContabilizaFolha", "Base Pis S/ Salários",
"Demonstrativo de Férias"). Marcados como **"sem vínculo"** e mantidos no roteiro.

## Verbas repetidas no roteiro
**Nenhuma.** Cada `Cod. Verba` aparece em uma única Ordem. A funcionalidade de "mostrar
todas as posições" existe no HTML mas, no dado atual, nunca dispara.

## Candidatos rejeitados
- **Número embutido em `Arquivo Formula`** (ex.: `FORMULA001_Salario` → 1): **não é** o ID da
  fórmula. Esse passo tem `ID Formula` = 506. O número no nome do arquivo é um índice
  sequencial de arquivo, não bate com nada do catálogo.
- **`ID Formula` ↔ `Código da Formula`**: parece casar 318, mas é **enganoso** —
  `Código da Formula` **não é único**: o valor `794` aponta para 9 verbas diferentes e `1196`
  para 2. Isso produz **8 passos divergentes** (Ordens 303–310) onde o join por fórmula
  retornaria a verba errada. Por isso `Cod. Verba` é superior.

## Divergência suspeita registrada
As Ordens **303–310** compartilham a mesma fórmula (794) mas são 8 verbas distintas
(cods 1304–1310 e 510). Pelo `Cod. Verba` cada uma resolve corretamente; o alerta é apenas
que confiar no ID de fórmula aqui daria resultado errado.

## Classificação em fases (heurística)
Sinais textuais da descrição têm prioridade sobre a natureza genérica da verba. Regras
avaliadas em ordem (primeira que casar vence); documentadas no topo de `gerar_roteiro_mensal.py`.
O campo `fase` é **editável** no JSON embutido para ajuste manual posterior.

Distribuição resultante: Contabilização 69 · Outros 61 · Médias e 13º 57 · Salário/Proventos 46
· Descontos 39 · Encargos 32 · Bases 30 · Férias 18 · Adicionais 11 · DSR 3 · Lançamentos 3.

> "Outros" (61) reúne casos genuinamente ambíguos (ex.: Quebra de Caixa, Cesta Básica,
> Previdência Privada, Benefícios) sem sinal textual claro — candidatos naturais a ajuste manual.
