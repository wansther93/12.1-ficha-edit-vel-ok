# Reformulação Visual e Ergonômica da Aba de Progresso (Ficha Técnica)

Plano arquitetural e visual para modernizar a aba de **Progresso** e a **Linha do Tempo** dentro da Ficha Técnica do Anime (`AnimeDetailModal.tsx`). O objetivo é substituir o layout confuso por uma experiência de streaming tátil, elegante e contínua no fundo preto puro (`#000000`), sem containers quadrados encaixados e preservando 100% das automações e funções existentes.

## User Review & Critical Decisions

> [!IMPORTANT]
> **Decisões críticas alinhadas com o usuário:**
>
> 1. **Zero Caixas Quadradas Pesadas (Sem Cards Dentro de Cards):**
>    - A linha do tempo será renderizada como uma sequência de **pills horizontais compactas e fluidas**, integradas de forma limpa ao fundo preto OLED, usando divisores sutis de 1px (`border-white/[0.06]`) e tipografia hierárquica.
> 2. **Substituição Definitiva do Botão "Assistir":**
>    - O projeto é um tracker/catálogo e não possui player de vídeo. Para temporadas inativas, a ação clara passa a ser **"Definir como Atual"** (com ícone tátil de troca).
>    - A temporada em andamento ganha o selo luminoso âmbar **"★ Temporada Atual"**.
> 3. **Conclusão Clara com Selo Esmeralda:**
>    - Para temporadas já vistas: selo esmeralda nítido **"✓ Concluída"** (interativo para alternar).
>    - Para temporadas pendentes: botão direto **"Marcar Concluída"**, sem botões misteriosos ou adivinhações.
> 4. **Controle de Episódios Integrado na Temporada Atual:**
>    - O controlador tátil (`-1 Ep`, contador em fonte tabular `Ep X / Y`, `+1 Ep` dourado e link `Digitar`) fica integrado **diretamente dentro do marco da temporada atual**, com barra de progresso suave em gradiente.
> 5. **Preservação da Separação Séries vs. Extras/Filmes:**
>    - Mantém a segmentação elegante em abas horizontais (`Séries (X)` / `Extras & Filmes (Y)`) para controlar a linha do tempo principal e as produções paralelas de forma independente.
> 6. **Zero Perda de Funções:**
>    - O botão `Gerenciar` (edição inline de nomes, total de episódios e recarregar árvore oficial), o slider de avaliação pessoal de estrelas (`★ 1 a 10`), o alerta comemorativo de avanço de temporada e todas as conexões com o Firestore permanecem intactos.

---

## 1. Overview & Core Concept

- **O que faz:** Transforma a visualização e gestão de temporadas e episódios de um anime na Ficha Técnica em uma linha do tempo intuitiva, moderna e rápida, permitindo que o usuário identifique em menos de 2 segundos:
  1. Em qual temporada ele está assistindo no momento;
  2. Em qual episódio dessa temporada ele parou;
  3. Quais temporadas/filmes já foram concluídos;
  4. Quais são as próximas temporadas disponíveis.
- **Público-alvo:** Usuários que acompanham animes longos ou com múltiplos lançamentos (*Demon Slayer, Attack on Titan, Slime, Fate, One Piece*) e querem controle cirúrgico sem poluição visual.
- **Valor Principal:** Elimina a confusão do termo "Assistir", remove o excesso de caixas e bordas pesadas, e entrega uma interface com fluidez de streaming nativo.

---

## 2. User Experience & Visual Design

### Estrutura Visual da Aba de Progresso (de cima para baixo):

```
┌─────────────────────────────────────────────────────────────────┐
│ 1. AVALIAÇÃO PESSOAL (Topo Limpo)                               │
│    ★ Sua Avaliação: 9.0 / 10   [───────●────────]   [Limpar]    │
├─────────────────────────────────────────────────────────────────┤
│ 2. CABEÇALHO DA LINHA DO TEMPO & SEGMENTOS                      │
│    [Linha do Tempo] • 2/4 concluídas    [Séries (3)] [Extras (1)]│
│                                              [Gerenciar ✎]      │
├─────────────────────────────────────────────────────────────────┤
│ 3. LINHA DO TEMPO EM PILLS COMPACTAS (Fundo Preto OLED)         │
│                                                                 │
│  [✓] Temporada 1 • 26 eps • Concluída                           │
│      [✓ Concluída]                    [Definir como Atual]      │
│  ─────────────────────────────────────────────────────────────  │
│  [★] Temporada 2 • Distrito do Entretenimento (ATUAL)           │
│      [ -1 ]  Episódio 7 / 11  [ +1 ]   [Digitar]                │
│      [======================>          ] 64% (4 eps restantes)  │
│      [Marcar Concluída]                                         │
│  ─────────────────────────────────────────────────────────────  │
│  [3] Temporada 3 • Vila dos Ferreiros • 11 eps                  │
│      [Pendente]                       [Definir como Atual]      │
└─────────────────────────────────────────────────────────────────┘
```

### Detalhamento dos Componentes:

1. **Slider de Avaliação (Topo):**
   - Linha suave flutuando no preto com estrela âmbar (`text-amber-400`), slider ergonômico de 1 a 10 (passo 0.5) e botão "Limpar".
2. **Segmentador Séries vs. Extras:**
   - Botões estilo segmented control (`px-3 py-1 text-xs rounded-lg`), permitindo alternar instantaneamente entre a saga principal e filmes/OVAs sem recarregar a tela.
3. **Pills Horizontais da Linha do Tempo:**
   - **Temporadas Concluídas:** Pill horizontal com texto esmaecido elegante (`text-zinc-300`), selo verde esmeralda `✓ Concluída` e botão sutil `Definir como Atual` (caso queira rever).
   - **Temporada Atual (Destaque Principal):** Borda suave em âmbar (`border-amber-400/40`), badge `★ Atual`, painel compacto com botões de toque generoso (hitbox $\ge 44\text{px}$) `-1 Ep`, contador tabular em destaque, `+1 Ep` dourado e barra de progresso em gradiente esmeralda.
   - **Próximas Temporadas:** Pill clean com identificador numérico, total de episódios previstos da API e botão direto `Definir como Atual`.
4. **Painel "Gerenciar" (Quando Ativado):**
   - Permite editar inline nomes, quantidade de episódios e sincronizar com a árvore oficial (`handleRefreshOfficialTreeInline`), aparecendo apenas quando o usuário clica em `Gerenciar`.

---

## 3. Key Product Decisions & Trade-Offs

- **Manutenção Estrita de Assinaturas e Event Handlers:**
  - Nenhuma função de backend ou prop do componente é alterada. As chamadas `onSwitchSeason`, `onToggleSeasonWatched`, `onIncrement`, `onDecrement`, `onUpdateRating` e `onUpdateEpisode` continuam sendo acionadas com as mesmas validações de segurança (prevenção de números negativos via `Math.max(0, ...)` e salvamento em tempo real no Firestore).
- **Sem Caixas Repetitivas (Zero Box-in-Box):**
  - Em vez de envolver cada temporada em um container de fundo cinza com sombra pesada, o design adota linhas de fluxo contínuo e pills ergonômicas com micro-bordas que respiram no fundo preto do app.
- **Apenas Onde Foi Pedido:**
  - O cabeçalho da Ficha Técnica (banner, capa, título, ano, status pills, botão de trailer, botão de notícias) e as demais abas (`Infos`, `Elenco`, `Músicas`, `Onde Ver`) **não sofrem nenhuma alteração visual ou lógica**, preservando 100% de estabilidade.

---

## 4. Technical Architecture & Component Mapping

```
┌────────────────────────────────────────────────────────────────────────┐
│                        AnimeDetailModal.tsx                            │
│                                                                        │
│  [ Banner + Capa + Título + Trailer + Status Pills ] (Inalterado)       │
│  [ Abas: Progresso (Ativa) | Infos | Elenco | Músicas | Onde Ver ]     │
│                                                                        │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │ Bloco: {activeTab === 'progress'}                                │  │
│  │                                                                  │  │
│  │  1. Personal Rating (Slider ★ 1-10)                              │  │
│  │  2. Timeline Header (Contador de Conclusão + Filtros TV/Extras)  │  │
│  │  3. Streamlined Horizontal Timeline Stream:                      │  │
│  │                                                                  │  │
│  │     • Concluded Seasons:                                         │  │
│  │       [ ✓ Concluída ] ───> toggleSeasonWatchedStatus()           │  │
│  │       [ Definir como Atual ] ───> onSwitchSeason()               │  │
│  │                                                                  │  │
│  │     • Active Season (Amber Highlight):                           │  │
│  │       [ -1 ] ───> decrementEpisode()                             │  │
│  │       [ Ep Atual / Total ] (Tabular Nums)                        │  │
│  │       [ +1 ] ───> incrementEpisode()                             │  │
│  │       [ Digitar ] ───> setEpisodeDirectly()                      │  │
│  │       [ Barra de Progresso Gradiente % ]                         │  │
│  │       [ Marcar Concluída ] ───> toggleSeasonWatchedStatus()      │  │
│  │                                                                  │  │
│  │     • Future/Upcoming Seasons:                                   │  │
│  │       [ Pendente ]                                               │  │
│  │       [ Definir como Atual ] ───> onSwitchSeason()               │  │
│  │                                                                  │  │
│  │  4. Season Manager Overlay (Inline Edit / Sync Tree)             │  │
│  └──────────────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────┘
```

- **Arquivos Envolvidos:**
  - `src/components/AnimeDetailModal.tsx`: Único arquivo a receber a lapidação visual cirúrgica no bloco `{activeTab === 'progress'}`.
  - `src/services/animeService.ts`: Chamado pelas props já existentes, sem necessidade de alterações na lógica de banco.
