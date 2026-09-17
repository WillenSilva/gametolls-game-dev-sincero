---
name: game-maker-assistente
description: "Metodologia de escrita e estruturação de jogos no GameMaker (GML) — arquitetura, sistemas e boas práticas. Use ao planejar, estruturar, programar, revisar ou dar consultoria sobre um jogo ou sistema de jogo no GameMaker Studio 2."
---

# Game Maker Assistente

Metodologia e catálogo de padrões para escrever e estruturar jogos no GameMaker Studio 2 (GML). Use esta skill para ajudar a planejar a arquitetura de um jogo novo, estruturar um sistema específico (movimento, câmera, inventário, save, diálogo etc.), revisar/depurar código GML existente, ou responder dúvidas de "como estruturar X nesse motor".

## Fluxo de trabalho geral

Para toda mecânica nova, siga o mesmo ciclo:

1. **Definir o objetivo** da mecânica antes de codar (o que ela resolve, não só como).
2. **Implementar uma primeira versão simples**, validada com `show_debug_message()`/`show_message()` em tempo real.
3. **Testar com um caso prático concreto** em cima do que acabou de ser implementado (não pular direto pra próxima feature).
4. **Revisar e corrigir bugs de integração** — a maioria dos bugs reais aparece quando dois sistemas já prontos se encontram (ex.: pulo + escada, dano + transição de sala), não dentro de um sistema isolado.
5. **Extrair código repetido para scripts/funções reutilizáveis** (`sc_maquina_estados`, `sc_utilidades` etc.) e **organizar objetos em pastas temáticas** (Player, Level, Sensores, Inimigos) assim que o projeto cresce.

## Arquitetura de máquina de estados

Duas abordagens, em ordem crescente de sofisticação — escolha conforme o tamanho do projeto:

- **Switch/string simples** (`estado = "parado"`, `switch(estado) { case "andando": ... }`), combinada com `event_inherited()` quando o objeto herda de um pai. Boa para protótipos e projetos menores.
- **Constructor reutilizável** (projetos maiores): um "molde" de estado como constructor —
 ```gml
 function estado() constructor {
 static inicia = function(){};
 static roda = function(){};
 static finaliza = function(){};
 }
 ```
 mais funções auxiliares centralizadas `inicia_estado()`/`troca_estado()` para trocar de estado de forma única em todo o projeto, e estados nomeados como funções do próprio objeto (`estado_tinta_entrar = function(){...}`, `estado_tinta_sair = function(){...}`). Prefira esta abordagem para qualquer jogo com mais de ~3-4 estados de personagem ou que vá crescer bastante.

Organize o Step do objeto principal em funções nomeadas chamadas em sequência (`checa_chao()`, `controles()`, `roda_estado()`, `ajusta_escala()`, `movimento_vertical()`) em vez de um bloco único — facilita adicionar QoL depois sem reescrever tudo.

## Movimento e colisão — escolha por gênero

- **Plataforma:** velocidade horizontal pela diferença de teclas (`velh = (_right - _left) * max_velh`), gravidade acumulada (`velv += grav`, com `max_velv`), checagem de chão a cada frame.
- **Top-down:** `point_direction` a partir do input, ou herança `obj_entidade` → `obj_inimigo_pai` → inimigo específico para compartilhar colisão/movimento.
- **Colisão sub-pixel** (evita atravessar paredes em alta velocidade ou "grudar" com folga): avançar 1 pixel por vez dentro de `while (!place_meeting(...))`, usando `sign(velh)` pra direção.
- **`move_and_collide(velh, velv, all, precisao)`:** alternativa nativa com resolução de colisão, retorna array de instâncias colididas — útil combinada com `switch` no `object_index` do primeiro item pra tratar rampas/plataformas móveis.
- **Escadas:** detectar via `collision_rectangle` contra uma layer de tiles dedicada (`lay_stair`); alterar lógica de colisão/movimento vertical enquanto o personagem está nela — é uma fonte clássica de bugs de integração com o pulo, corrija-os numa etapa própria em vez de tentar prevenir tudo de antemão.

### Checklist de QoL de plataforma ("juice") — aplique sempre que o jogo tiver pulo
Coyote time (timer que permite pular logo depois de sair da borda), jump buffer (timer que registra o clique de pulo um pouco antes de tocar o chão), corner correction (empurrar sutilmente o personagem pra longe da quina em vez de travar o pulo), pulo duplo (contador `qtd_pulos`/`qtd_pulos_atual`), e diferenciação andar/correr (`velh_walk`/`velh_run`). Todos esses sistemas guardam seus próprios timers como variáveis de instância e se integram à máquina de estados existente — não são sistemas isolados.

## Câmera

Objeto de câmera dedicado (`obj_cam`). Para cutscenes, faça a câmera seguir um Path pré-desenhado no editor de Rooms, sincronizando o estado do player (parado) com o estado da câmera (seguindo o path). QoL adicional: permitir trocar o alvo que a câmera segue (cutscenes/eventos especiais), screenshake, e um "efeito de retorno" suave da câmera ao jogador depois de focar em outro ponto.

## Animação

Arrays de sprite por estado (parado/andando/ataque) e por direção, usando `xscale` pra espelhar em vez de duplicar sprites. Para transições suaves de valores (cor, alpha), prefira o editor nativo de **Animation Curve** a calcular a interpolação manualmente. Para animações por timeline/keyframe (golpes especiais, título animado), use o **Sequence Editor**.

## Diálogo e texto

Caixa de diálogo custom com `draw_text`/fonte própria posicionada por `display_get_gui_height()`, ou uma biblioteca de texto pronta para recursos como efeito de máquina de escrever e ondulação/tremida no texto. Para caixas que se ajustam ao conteúdo, use `string_height_ext` pra medir o texto e calcular a borda. Para janelas redimensionáveis sem distorcer bordas, use **Nine Slice** no sprite.

## Sensores e triggers reutilizáveis

Um único objeto sensor genérico que recebe um **método customizado na criação da instância** (`metodo_generico = function(_player){...}` definido no Creation Code de cada instância), permitindo reaproveitar o mesmo objeto para disparar comportamentos diferentes em pontos diferentes do nível, em vez de criar um objeto sensor por efeito.

## Inventário, itens e dano

- Inventário em grid: `ds_grid_create(colunas, linhas)`; item seguindo o mouse com `draw_sprite_stretched(..., mouse_x, mouse_y)`; texto de item com contorno via duas passadas de `draw_text_ext_transformed` (uma preta, uma branca).
- Dano: objeto `obj_dano` guardando uma `ds_list` de quem já foi atingido por aquele golpe (evita bater duas vezes no mesmo ataque) e uma referência ao causador do dano (`obj_player.id`).
- Vida/HUD em corações: `ganha_vida()`/`ganha_coracao()` usando `min()` pra não passar do máximo; desenhar percorrendo `vida_max` em loop, sobrepondo célula "vazia" com a quantidade atual.

## Estruturas de dados nativas — regra de ouro

`ds_list`, `ds_stack` (LIFO), `ds_queue` (FIFO), `ds_map` (chave-valor), `ds_grid` (2D) **nunca são destruídas sozinhas** — sempre chame `ds_*_destroy()` no evento Clean Up de quem as criou, ou o jogo acumula bugs, trava e fica lento com o tempo. A mesma regra vale para sistemas de partículas criados via código (`part_type_destroy`, `part_emitter_destroy`, `part_system_destroy`) e para surfaces. Para dados relacionados sem precisar de um object asset, prefira **structs** (literais ou via **constructor function** com `new`, com `static` para membros compartilhados entre instâncias).

## Save / load

Padrão recomendado: montar uma **struct** com os dados a salvar → `json_stringify(_struct)` → escrever num buffer (`buffer_create`, `buffer_write`) → `buffer_save(_buff, nome_do_arquivo)` → `buffer_delete`. Suportar múltiplos slots de save associando o nome do arquivo a um número/variável global (`global.save_atual`), com tela de seleção ("Save 1/2/3") e opção de apagar um save. Um inventário em `ds_grid` precisa ser convertido pra array 2D antes de serializar.

## Level design

- Organize por **layers/tiles** (piso, decoração, paredes) em vez de um objeto por bloco de cenário; use **autotile** pra escolher automaticamente a peça certa (canto/borda/centro) por vizinhança.
- Geração procedural: tamanho de sala/mapa como base fixa × fator aleatório (`irandom_range`), preenchendo uma grid; para formas orgânicas (cavernas), o algoritmo clássico é o **drunken walk** (caminhada aleatória).
- Checklist de armadilhas de design a evitar em toda sala nova: **blind jumps** (pulos que o jogador não consegue prever visualmente), **soft locks** (o jogador fica preso sem conseguir progredir nem voltar) e **bottlenecks** (gargalo que força todo mundo pelo mesmo caminho estreito).

## Otimização e organização de projeto

- Variáveis globais (`global.x`) ficam alocadas o jogo inteiro — use com moderação; variáveis de instância só existem enquanto o objeto existe.
- Use **macros** (`#macro GRAVIDADE .2`) em vez de números soltos, e **enums** em vez de valores mágicos.
- Compare o custo de estruturas de dados/abordagens antes de escolher (ex.: Evento de Colisão nativo vs. chamar `place_meeting` manualmente no Step têm custos diferentes conforme o caso).
- Ative/desative instâncias fora da câmera para poupar processamento — mas avalie caso a caso: desativar interrompe eventos e pode gerar bugs de sincronização se o objeto precisa continuar agindo fora de tela.
- Use `show_debug_message()` e o profiler nativo para medir gargalos com números reais antes de otimizar "no achismo" (ex.: laços aninhados repetindo a room célula a célula são um suspeito clássico).
- Configure **Texture Groups** pra controlar quais sprites ficam juntos na VRAM.

## Integração com GMS MCP

Sempre que o servidor MCP `gms-mcp` (https://github.com/Ampersand-Game-Studios/gms-mcp) estiver conectado na sessão, prefira suas ferramentas em vez de pedir pro usuário colar código manualmente no editor — ele permite ler e editar o projeto GameMaker (`.yyp`) diretamente pelo Claude.

- **Verificar disponibilidade primeiro:** chame `gm_capabilities` e `gm_project_info` antes de assumir que o MCP está presente ou de tentar usá-lo; se as ferramentas não aparecerem na sessão, trate como indisponível e volte ao fluxo manual (dar o código GML pra colar).
- **Perfis de permissão:** o servidor roda em `safe` (somente leitura — inspeção de projeto, assets, rooms, objetos, referências, navegação de código) ou `full` (leitura + criação/edição de assets, instâncias em rooms, sprites, áudio, build via Igor, bridge TCP para interação com o jogo em execução). Confirme com o usuário antes de qualquer operação de escrita (criar/editar objeto, sala, asset) se não estiver claro que o perfil `full` foi autorizado — segue a mesma lógica de confirmação de ações com efeito colateral já usada no resto do fluxo.
- **Setup (uma vez por projeto), se o usuário pedir ajuda pra configurar:**
  1. Pré-requisitos: Python 3.10+, um projeto GameMaker com `.yyp`, `pipx install gms-mcp` (mais `pipx inject gms-mcp Pillow` se precisar de suporte a imagem).
  2. Gerar a configuração do cliente a partir da pasta do projeto: `gms-mcp-init --client claude-code --scope workspace --action app-setup` (somente leitura) ou `--profile full` (leitura + escrita).
  3. Reiniciar a conexão MCP do cliente depois do setup.
  4. Verificar com `gms-mcp doctor --project`.
- **Uso prático:** com o MCP ativo, aplique a mesma metodologia desta skill (máquina de estados, herança, hordas via struct, etc.) mas execute as mudanças diretamente nos assets do projeto via `gms-mcp` em vez de descrever blocos de código soltos — e ainda assim explique cada mudança em termos do sistema (o que ela resolve), não só o que foi escrito.

## Controle de versão (Git)

Fluxo mínimo recomendado desde o início de qualquer projeto: `git init` no projeto → `git add`/`git commit` a cada marco funcional → `git log` pra consultar histórico → desfazer mudanças não commitadas quando um experimento dá errado → `git revert` para desfazer um commit já feito com segurança (sem reescrever histórico).

## Áudio e gamepad (polimento)

- Áudio 3D: `audio_emitter_create`, Listener (geralmente ligado à câmera/jogador), falloff de volume por distância, orientação pra efeito Doppler. Fade de volume com `audio_sound_gain(som, volume_alvo, duracao_ms)`.
- Gamepad: eixos analógicos (`gamepad_axis_value`) espelhando a mesma lógica do teclado, tratamento assíncrono de conectar/desconectar via `async_load` + `switch` nos eventos `"gamepad discovered"`/`"gamepad lost"`, com fallback pro teclado quando desconecta.