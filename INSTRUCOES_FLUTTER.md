# Guia de Migração: Recriando o "Meu Fut" em Flutter

Bruno, este guia foi criado para te ajudar a traduzir a lógica e o design do nosso projeto web (Next.js/React) para um aplicativo nativo usando Flutter e a linguagem Dart.

**Importante:** A migração não é uma simples cópia de código. É um processo de recriação, onde a lógica de negócios é "traduzida" e a interface do usuário é reconstruída com as ferramentas do Flutter.

---

## Passo 1: Configurar o Ambiente Flutter

Antes de começar, certifique-se de que você tem o Flutter e o Dart instalados na sua máquina.

1.  **Instale o Flutter:** Siga o guia oficial em [docs.flutter.dev](https://docs.flutter.dev/get-started/install).
2.  **Crie um Novo Projeto:** Use o comando `flutter create meu_fut_flutter`.
3.  **Estrutura de Pastas:** Dentro da pasta `lib` do seu novo projeto Flutter, você pode criar uma estrutura similar à nossa:
    *   `lib/models/`: Para as classes de dados (o equivalente ao nosso `types.ts`).
    *   `lib/data/`: Para os dados simulados (o equivalente ao `mock-data.ts`).
    *   `lib/screens/`: Para cada página principal do aplicativo (Dashboard, Elenco, Tática, etc.).
    *   `lib/widgets/`: Para componentes reutilizáveis (como os nossos `Card`s, botões, etc.).

---

## Passo 2: Traduzir a Lógica e os Dados (O "Backend" do App)

Esta é a parte mais direta da migração. A lógica de negócios é a mesma, apenas a sintaxe muda.

### 2.1. Modelos de Dados (`/src/lib/types.ts` -> `lib/models/`)

Cada `type` no nosso arquivo `types.ts` deve ser convertido para uma `class` em Dart.

**Exemplo (Jogador):**

Nosso `Player` em TypeScript:
```typescript
export type Player = {
  id: number;
  name: string;
  position: 'Goleiro' | 'Defensor' | 'Meio-campista' | 'Atacante';
  // ... outros campos
};
```

O equivalente em Dart (`lib/models/player.dart`):
```dart
enum PlayerPosition { goleiro, defensor, meioCampista, atacante }

class Player {
  final int id;
  final String name;
  final PlayerPosition position;
  final String avatarUrl;
  final int shirtNumber;
  int gamesPlayed;
  int goals;
  // ... outros campos

  Player({
    required this.id,
    required this.name,
    required this.position,
    required this.avatarUrl,
    required this.shirtNumber,
    this.gamesPlayed = 0,
    this.goals = 0,
    // ...
  });
}
```

**Sua Tarefa:** Crie um arquivo `.dart` para cada entidade (`Player`, `Match`, `Bet`, etc.) e converta os tipos TypeScript para classes Dart.

### 2.2. Dados Simulados (`/src/lib/mock-data.ts` -> `lib/data/`)

Nossos arrays de dados em `mock-data.ts` podem ser recriados como listas em Dart.

**Exemplo:**

Em `mock-data.ts`:
```typescript
export const players: Player[] = [
  { id: 1, name: 'Adriano Imperador', ... },
  // ...
];
```

Em `lib/data/mock_data.dart`:
```dart
import 'package:meu_fut_flutter/models/player.dart';

final List<Player> mockPlayers = [
  Player(id: 1, name: 'Adriano Imperador', position: PlayerPosition.atacante, ...),
  // ...
];
```

---

## Passo 3: Reconstruir a Interface do Usuário (O "Frontend")

Esta é a parte mais criativa e trabalhosa. Cada componente React será substituído por um ou mais **Widgets** do Flutter.

### 3.1. Estilo e Tema (`globals.css` -> `ThemeData`)

Nossa identidade visual (cores, fontes) deve ser centralizada no `ThemeData` do Flutter, geralmente no seu arquivo `main.dart`.

**Exemplo:**

Nossas cores em `globals.css`:
```css
:root {
  --background: 222 84% 5%;
  --primary: 148 100% 50%;
  --destructive: 0 100% 66%;
}
```

Sua configuração de tema no `MaterialApp` do Flutter:
```dart
MaterialApp(
  theme: ThemeData(
    scaffoldBackgroundColor: Color(0xFF0A0D15), // Azul/carvão
    primaryColor: Color(0xFF00FF87), // Verde neon
    colorScheme: ColorScheme.dark().copyWith(
      primary: Color(0xFF00FF87),
      secondary: Color(0xFF00FF87),
      error: Color(0xFFFF4D4D), // Vermelho neon
      background: Color(0xFF0A0D15),
    ),
    // ... fontes e outros estilos
  ),
  home: LoginPage(),
);
```

### 3.2. Componentes e Widgets (`/src/components` -> `lib/widgets` e `lib/screens`)

Cada componente `.tsx` deve ser analisado e reconstruído com Widgets.

*   **`Card`:** Use o widget `Card` do Flutter, estilizado com `BoxDecoration` para criar o efeito de vidro (`cyber-glassmorphism`). Você pode usar o pacote `glass_kit` ou `blur` para o efeito de desfoque no fundo.
*   **`Button`:** Use `ElevatedButton`, `TextButton` ou `OutlinedButton` e personalize o `style` para combinar com nosso design.
*   **`Table` em Elenco:** Use um `ListView.builder` que gera uma lista de `Card`s ou `ListTile`s customizados para cada jogador.
*   **`FieldView` na Prancheta Tática:** Esta é a parte mais complexa. Você pode usar um `Stack` para sobrepor os avatares dos jogadores sobre uma imagem de fundo do campo. Para o "arrastar e soltar", o widget `Draggable` do Flutter é a ferramenta perfeita.

**Fluxo de Trabalho Sugerido:**

1.  **Comece pelo Login:** Crie a tela de login (`login_screen.dart`) com `TextField`s para as senhas e um `DropdownButton` para a seleção de jogador.
2.  **Construa o Layout do Dashboard:** Crie um `Scaffold` com um `Drawer` (para a navegação lateral no Flutter) ou um `BottomNavigationBar`.
3.  **Crie uma Página de Cada Vez:** Pegue uma página do nosso projeto, como a "Elenco" (`/dashboard/squad`). Analise os componentes React e recrie a mesma estrutura visual e funcional usando os widgets do Flutter.

Este guia é seu ponto de partida. O processo de recriar o app em Flutter será desafiador, mas muito recompensador. A lógica e o design que já construímos servirão como um mapa claro para o sucesso do seu aplicativo nativo. Boa sorte!