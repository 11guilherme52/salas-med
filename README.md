# Salas Med

Web app (PWA) para a **conferência diária das salas de aula de Medicina** da Censupeg (2º Andar › Bloco Amarelo), semestre 2026-2.

Mostra o calendário de uso das salas, a ordem de conferência de cada dia e guarda as anotações e fotos de cada sala no próprio celular. No fim do dia, exporta tudo num ZIP para gerar os relatórios em PDF.

**Acesse:** https://11guilherme52.github.io/salas-med/

---

## Funcionalidades

- **Calendário** (tela principal): mês a mês, com as cores da Semana 1 e da Semana 2 e bolinhas indicando quantas salas têm aula às 07:10.
- **Dia**: ao tocar numa data, mostra todas as aulas de cada sala, um resumo (salas com aula, aulas às 07:10, salas livres) e o botão **Modo de conferência**.
- **Modo de conferência**: lista as salas em ordem de prioridade (quem tem aula mais cedo primeiro, depois as livres e, por último, a 212), com o horário-limite de cada uma e a situação da conferência.
- **Anotações por sala**: texto (ou ditado pelo microfone do teclado), fotos e status (*Conferida OK*, *Com pendências*, *Não conferida*). Cada anotação pode ser apagada.
- **Alteração do dia**: campo livre para observações gerais.
- **Exportar conferência (ZIP)**: gera um arquivo com o resumo em texto e as fotos do dia, pelo menu de compartilhar do iPhone ou como download.
- **Funciona offline** depois do primeiro acesso e tem modo escuro automático.

## Salas e turmas

| Sala | Turma | Semestre |
|------|-------|----------|
| 202 | T1 | 4º |
| 204 | T3 | 3º |
| 206 | T4 | 2º |
| 208 | T5 | 2º |
| 210 | T2 | 4º |
| 212 | — | backup (sem turma fixa) |

As Semanas 1 e 2 se alternam de 03/08 a 18/12/2026. Os horários vêm dos cronogramas das turmas T1 a T5. Aulas fora da sala (hospital, UBS) contam como sala livre. Os intervalos (09:40–09:50, 12:20–13:20 e 15:50–16:00) ficam livres.

## Instalar no iPhone

1. Abra o link no **Safari**.
2. Toque em **Compartilhar** → **Adicionar à Tela de Início**.
3. O ícone "Salas Med" abre em tela cheia, como um app.

Depois de uma atualização, abra o app com internet uma vez para ele baixar a versão nova.

## Fluxo da conferência

1. Calendário → dia → **Modo de conferência**.
2. Em cada sala, anote o que foi conferido, o que mudou e o que ficou pendente, e marque o status.
3. No fim, toque em **Exportar conferência (ZIP)** e envie o arquivo no chat do Claude.
4. O Claude lê as anotações e gera os relatórios em PDF: um checklist por sala e o consolidado.

### Conteúdo do ZIP

```
Conferencia_AAAA-MM-DD.zip
├── conferencia.txt     # cabeçalho, alteração do dia e cada sala com status e anotações
└── fotos/
    ├── sala_212_01.jpg
    └── ...
```

Exemplo do `conferencia.txt`:

```
CONFERÊNCIA DE SALAS - MEDICINA
Data: 17/11/2026 (Terça) - Semana 2
Local: 2º Andar › Bloco Amarelo
Responsável Técnico: Guilherme Resende

==================================================
SALA 212 (backup)
Status marcado: Com pendências
[07:42] Sala 212 funcionando, tela continua rasgada
[07:43] [foto: fotos/sala_212_01.jpg]
```

## Onde ficam os dados

As anotações e fotos ficam **só no aparelho** (IndexedDB do navegador). Nada é enviado para servidor. Por isso:

- exporte o ZIP todo dia;
- apagar os dados do Safari ou desinstalar o app apaga as anotações;
- as anotações feitas num aparelho não aparecem em outro.

## Estrutura do projeto

```
index.html             # app completo: HTML, CSS, JS e os horários das salas
manifest.webmanifest   # nome, ícones e modo tela cheia
sw.js                  # service worker (cache para funcionar offline)
icons/                 # ícones do app
vendor/jszip.min.js    # biblioteca para gerar o ZIP (JSZip 3.10.1)
```

Sem build e sem dependências externas em tempo de execução: é um site estático servido pelo GitHub Pages a partir da branch `main`.

## Atualizar os horários (próximo semestre)

Os horários estão no objeto `DATA` dentro do `index.html`, organizados por sala → semana (1 ou 2) → dia → blocos de aula (`start`, `end`, `subj`, e `fora`/`where` para aulas fora da sala). As datas do semestre estão em `START` e `END`.

Para um novo semestre:

1. Atualize `DATA`, `START`, `END` e a lista `ROOMS` (sala, turma, semestre).
2. Troque a versão do cache em `sw.js` (`salas-med-v3` → `v4`) para os aparelhos baixarem a versão nova.
3. Faça o commit na `main`; o GitHub Pages publica em um ou dois minutos.

## Observação sobre privacidade

O repositório e o site são públicos: qualquer pessoa com o link vê os horários das salas. As anotações e fotos da conferência **não** ficam no repositório nem no site, só no aparelho de quem usa.
