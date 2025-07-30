
# Prompt Mestre: Recriando o Aplicativo "Meu Fut"

## 1. Visão Geral e Conceito

Você é um desenvolvedor de software expert em Next.js, React, TypeScript e Tailwind CSS. Sua missão é construir um aplicativo web completo chamado **"Meu Fut"**.

**Conceito:** Uma plataforma de gestão para times de futebol amador com uma estética "cyberpunk futurista". O aplicativo servirá como um hub central para administradores (chamados de "Diretores") e "Jogadores", integrando gerenciamento de elenco, agendamento de partidas, confirmação de presença, finanças do clube, um sistema de apostas virtuais e análises táticas potencializadas por IA.

**Público-Alvo:** Administradores e jogadores de times de futebol amador que buscam organização, engajamento e uma experiência moderna.

---

## 2. Identidade Visual e Estilo (Inspirado em Cyber-Glassmorphism)

A estética é um pilar do projeto. Siga estas diretrizes rigorosamente.

### 2.1. Paleta de Cores (HSL para `globals.css`)
- **Background (`--background`):** `222 84% 5%` (Azul/carvão muito escuro).
- **Primary (`--primary`):** `148 100% 50%` (Verde neon para elementos interativos, botões, links, e indicadores de sucesso).
- **Accent/Destructive (`--accent`, `--destructive`):** `0 100% 66%` (Vermelho neon para alertas, erros, e ações de exclusão).
- **Cards/Popups (`--card`):** `224 71% 10%`. Este será a base para o efeito de "vidro".
- **Bordas (`--border`):** `148 100% 25%` (Verde neon mais escuro para as bordas dos cards).

### 2.2. Tipografia
- **Títulos e Destaques (`font-headline`):** Use a fonte **Orbitron**, negrito. Deve ser aplicada em cabeçalhos, placares e valores importantes para um visual de "placar digital".
- **Corpo do Texto (`font-sans`):** Use a fonte **Geist Sans** para parágrafos, descrições e textos gerais para garantir legibilidade e uma aparência moderna.

### 2.3. Efeitos Visuais e Animações
- **Brilho Neon:** Crie uma classe utilitária `text-glow` em `globals.css` que aplique `text-shadow` com a cor `--primary` para simular o brilho de um letreiro neon.
- **Cyber-Glassmorphism:** Crie uma classe utilitária `cyber-glass` em `globals.css` para ser aplicada em `Card`s e `Dialog`s. Ela deve combinar:
  - `background-color`: `hsl(var(--card) / 0.6)` (60% de opacidade).
  - `backdrop-filter`: `blur(15px) saturate(180%)`.
  - `border`: `1px solid hsl(var(--border) / 0.2)` (20% de opacidade).
  - `box-shadow`: Um `shadow-lg` padrão.
  - `transition`: Adicione uma transição suave para que, no `:hover`, a opacidade da borda aumente para `50%` e o `box-shadow` se intensifique com a cor primária.
- **Animações de Entrada:** Use a biblioteca `tailwindcss-animate` para que os elementos surjam suavemente nas páginas (ex: `animate-in fade-in-50`).

---

## 3. Arquitetura e Estrutura de Arquivos

Use **Next.js com App Router** e **TypeScript**.

- **`src/app/`**: Rotas principais.
  - **`(public)/`**: A rota raiz (`/`) com a `page.tsx` (landing page) e a rota `/login/page.tsx`.
  - **`dashboard/`**: Grupo de rotas protegidas.
    - `layout.tsx`: Layout principal do painel, que renderiza uma `SidebarNav` (desktop) e uma `MobileNav` (móvel). Este layout é responsável por verificar a autenticação do usuário.
    - `page.tsx`: Página inicial do dashboard.
    - `[feature]/page.tsx`: Cada funcionalidade principal terá sua própria subpasta e página (ex: `squad`, `betting`, `tactics`, `finances`, etc.).
- **`src/components/`**: Componentes React.
  - **`ui/`**: Componentes genéricos da biblioteca **ShadCN** (ex: Button, Card, Dialog, etc.). **Instale e configure todos os componentes listados no projeto base.**
  - **`dashboard/`**: Componentes específicos para as páginas do dashboard. Eles devem conter a lógica do cliente e interações (ex: `squad/squad-client.tsx`, `tactics/tactics-client.tsx`).
  - **`icons/`**: Ícones SVG customizados (ex: `SoccerFieldSvg`, `FutsalCourtSvg`).
- **`src/lib/`**: Lógica de negócios e dados.
  - `types.ts`: Definições TypeScript para todas as entidades (`Player`, `Match`, `Bet`, `Championship`, etc.). Crie tipos detalhados para cada entidade do projeto.
  - `mock-data.ts`: Dados simulados para popular o aplicativo. Inclua um array de `players` e `matches` com dados realistas. Exporte também dados para `transactions` e `championships`.
  - `mock-responses.ts`: Funções que simulam respostas de uma IA, como `getTacticalAnalysis` e `getPlayerTactic`.
  - `utils.ts`: Funções utilitárias, principalmente a função `cn` para mesclar classes do Tailwind.
  - `firebase.ts`: Configuração e inicialização do cliente Firebase (mesmo que com chaves de exemplo).
- **`src/hooks/`**: Hooks React customizados.
  - `use-auth.ts`: Hook para gerenciar o estado de autenticação. Ele deve ler/escrever no `localStorage` e prover as funções `login` e `logout`.
  - `use-toast.ts`: Hook para exibir notificações (toasts).
  - `use-mobile.ts`: Hook para detectar se o dispositivo é móvel com base na largura da tela.

---

## 4. Estrutura de Dados (em `lib/types.ts`)

Defina tipos claros e robustos para:

- **`Player`**: Deve incluir `id`, `name`, `position`, `shirtNumber`, `avatarUrl`, e estatísticas como `gamesPlayed`, `goals`, `assists`, `credits` (para apostas), `attendances`, `absences`, `penaltyGoals`, e `manOfTheMatchAwards`.
- **`Match`**: Deve incluir `id`, `type` ('match' ou 'training'), `opponent`, `date`, `time`, `location`, `status` ('upcoming' ou 'past'), `modality` ('Campo', 'Society', 'Futsal'), `formation`, `lineup` (mapeamento de `playerId` para status), `confirmedPlayers` (array de `playerId`), `declinedPlayers`, `score`, `goalscorers`, e `ratings` (para notas pós-jogo).
- **`Bet`**: Deve incluir `id`, `matchId`, `userId`, `type` ('score' ou 'top_scorer'), `betValue`, `amount`, e `status`.
- **`Championship`**: Deve incluir `id`, `name`, `year`, `finalPosition`, `highlights` (texto sobre a campanha) e `standouts` (array de jogadores destaque com descrição).
- **`FinancialTransaction`**: `id`, `type` ('deposit', 'withdrawal', 'bet_cut'), `description`, `amount`, `date`.

---

## 5. Implementação Passo a Passo por Funcionalidade

### 5.1. Autenticação e Login (`/login` e `useAuth`)
1.  **Chave de Acesso:** A tela de login deve primeiro pedir um "ID do Time". Essa chave será salva no `localStorage` e usada como prefixo para todas as outras chaves de armazenamento, isolando os dados de diferentes times.
2.  **Portal de Acesso:** Uma vez que o ID do time está ativo, o usuário pode escolher entre logar como "Diretor" ou "Jogador".
3.  **Lógica de Senha:**
    -   **Diretor:** Usa uma senha de administrador (`Foco` por padrão).
    -   **Jogador:** Seleciona seu nome em um `<Select>` e insere a senha do time (`Disciplina` por padrão).
4.  **`useAuth` Hook:**
    -   Deve ler os dados do `localStorage` (`meu_fut_auth`).
    -   Deve validar se o `teamId` no objeto de autenticação corresponde ao `teamId` ativo.
    -   Deve prover as funções `login` e `logout` que manipulam o `localStorage`.
5.  **UI:** Use o componente `ProgressDialog` para mostrar uma tela de sucesso animada antes de redirecionar para o dashboard.

### 5.2. Layout do Dashboard (`/dashboard/layout.tsx`)
1.  **Proteção de Rota:** O layout deve usar o hook `useAuth` para verificar se um usuário está logado. Se não estiver, redirecione para `/login`. Exiba um layout de `Skeleton` enquanto carrega.
2.  **Navegação:**
    -   **Desktop:** Renderize uma `SidebarNav` fixa à esquerda.
    -   **Mobile:** Renderize um `Header` com um botão de menu (hambúrguer) que abre uma `MobileNav` em um `Sheet`.
3.  **Menus Dinâmicos:** A `SidebarNav` e a `MobileNav` devem exibir links diferentes com base na `role` do usuário ('admin' ou 'player').

### 5.3. Gerenciamento de Elenco (`/dashboard/squad`)
-   **Guarda de Rota:** A página deve ser acessível apenas para "Diretores" (`AdminGuard`).
-   **Componente Principal:** `SquadClient`.
-   **Funcionalidades:**
    1.  **Tabela de Jogadores:** Use o componente `Table` da ShadCN para listar todos os jogadores com avatar, nome, posição, número e estatísticas básicas.
    2.  **Adicionar Jogador:** Um `Dialog` deve abrir um formulário para adicionar um novo jogador. O avatar deve ser convertido para Data URL e salvo.
    3.  **Visualizar Card:** Clicar em um jogador na tabela deve abrir um `Dialog` com o `PlayerCollectibleCard` daquele jogador.
    4.  **Excluir Jogador:** Implemente uma ação de exclusão com um `AlertDialog` de confirmação.

### 5.4. Prancheta Tática (`/dashboard/tactics`)
-   **Guarda de Rota:** Acesso restrito a "Diretores".
-   **Componente Principal:** `TacticsClient`.
-   **Funcionalidades:**
    1.  **Seleção:** Permita que o admin selecione uma partida futura e uma formação tática (`4-4-2`, `4-3-3`, etc.) compatível com a modalidade do jogo.
    2.  **Lista de Elenco:** Liste todos os jogadores com `RadioGroup` para definir o status: "Titular", "Reserva", "Nenhum". O sistema deve impedir que mais titulares que o permitido pela modalidade sejam selecionados.
    3.  **Campo Visual (`FieldView`):**
        -   Renderize um SVG do campo de futebol/quadra de futsal.
        -   Posicione os avatares dos jogadores titulares no campo com base na formação.
        -   Permita arrastar e soltar (`drag-and-drop`) os jogadores para posições personalizadas.
    4.  **Análise Tática por IA:**
        -   Um botão "Analisar com IA" deve chamar uma função (do `mock-responses.ts`) para gerar uma análise geral da escalação, exibida em um `Card`.
        -   Clicar em um jogador no campo deve exibir um `IndividualTacticCard` com instruções ofensivas e defensivas específicas para aquela posição e formação.

### 5.5. Armazenamento de Dados
-   **Simulação de Reatividade:** Como não há um backend real, use o `localStorage` para persistir os dados.
-   **Chaves por Time:** Todas as chaves do `localStorage` (jogadores, partidas, senhas) devem ser prefixadas com o `teamId` ativo para simular o isolamento de dados.
-   **Evento de Atualização:** Crie um evento customizado (`teamDataUpdated`). Dispare este evento sempre que dados importantes (como a lista de jogadores) forem alterados. Outros componentes devem ouvir este evento para recarregar os dados e manter a UI sincronizada.
