# GameTolls Game Dev Sincero

Marketplace de skills do canal **GameTolls Game Dev Sincero** para quem cria jogos no GameMaker Studio 2 com apoio de IA (Claude Code).

## Primeiro passo

Copie o prompt abaixo e cole numa conversa nova do Claude Code — ele mesmo te leva a instalar o marketplace, configurar o `gms-mcp` (obrigatório pro resultado final) e fechar um GDD enxuto da sua ideia antes de começar a codar:

```
Quero configurar meu ambiente de game dev com a skill game-maker-assistente antes de começar a criar. Me guia pelos passos, na ordem, confirmando cada um comigo antes de seguir pro próximo:

1. Confirme que a skill "game-maker-assistente" do marketplace GameTolls Game Dev Sincero está instalada e ativa nesta sessão (se eu ainda não tiver rodado, me dê os dois comandos certos: /plugin marketplace add e /plugin install).

2. O servidor MCP gms-mcp (https://github.com/Ampersand-Game-Studios/gms-mcp) é obrigatório pro resultado final — sem ele eu só recebo código pra colar manualmente, em vez do Claude editando meu projeto diretamente. Verifique se ele está conectado nesta sessão; se não estiver, me guie pela instalação: pré-requisitos (Python 3.10+, projeto GameMaker com .yyp), pipx install gms-mcp, e gms-mcp-init na pasta do meu projeto com o perfil "full" (leitura + edição do projeto). Depois, me lembre de reiniciar a conexão MCP e valide com gms-mcp doctor --project.

3. Depois que os dois estiverem prontos, rode uma checagem final: confirme que a skill está carregando e que o gms-mcp está respondendo (chamando gm_capabilities / gm_project_info) e me diga em uma frase o que já está pronto pra eu usar.

4. Antes de eu pedir um jogo, mude para o modo de planejamento e me ajude a construir um GDD enxuto do meu jogo. Não vá direto pra código ou estrutura de objetos — primeiro organize minha ideia cobrindo, de ponta a ponta, estas categorias (pergunte só o que eu não tiver respondido):
   1. Pitch/conceito central — em 1-2 frases, o que é o jogo
   2. Pilares de design — 3-4 palavras/princípios que toda decisão futura precisa respeitar
   3. Gênero e referências — jogos parecidos, o que copiar e o que evitar
   4. Core loop — o que o jogador faz repetidamente, moment-to-moment
   5. Fantasia do jogador — o que ele sente/é dentro do jogo
   6. Escopo e plataforma — protótipo vs. jogo completo, PC/mobile, tamanho de conteúdo
   7. Arte/áudio — estilo visual e sonoro de referência
   8. Narrativa/ambientação — se tem, quanto peso tem
   9. Progressão e conteúdo — níveis, upgrades, quanto dura

Só depois do GDD enxuto estar fechado, começe a me ajudar a estruturar a arquitetura e os sistemas do jogo no GameMaker.
```

## O que tem aqui

- **game-maker-assistente** — metodologia de escrita e estruturação de jogos no GameMaker (GML): arquitetura, máquina de estados, movimento/colisão, câmera, save/load, otimização e mais.

## Como instalar

Se preferir instalar manualmente em vez de colar o prompt acima, dentro do Claude Code rode estes dois comandos (um de cada vez):

```
/plugin marketplace add WillenSilva/gametolls-game-dev-sincero
/plugin install game-maker-assistente
```

Depois disso a skill fica disponível automaticamente sempre que você pedir ajuda com arquitetura, sistemas ou código GML no GameMaker.

## gms-mcp — obrigatório para o resultado final

O [gms-mcp](https://github.com/Ampersand-Game-Studios/gms-mcp) (servidor MCP de terceiros que dá ao Claude acesso direto ao seu projeto `.yyp`) é parte do fluxo completo desta skill: sem ele, o Claude só consegue te dar código pra colar manualmente no editor, em vez de ler e editar o projeto diretamente. Para o resultado pretendido (o Claude estruturando e editando seu jogo de verdade), instale e conecte o `gms-mcp` — o prompt do "Primeiro passo" já cobre esse setup.

## Segurança

Esta skill é **apenas texto/instruções** (Markdown) que o Claude lê para saber como te ajudar:

- Não contém scripts executáveis, hooks automáticos nem MCP servers embutidos.
- Não faz chamadas de rede, não acessa arquivos fora do seu projeto e não pede nenhuma credencial, chave de API ou senha.
- Não é instalado nada no seu sistema além do arquivo `SKILL.md` em si.
- O `gms-mcp` mencionado acima é um projeto de terceiros, instalado e conectado por você separadamente — a skill apenas sabe orientar a instalação e usá-lo quando presente na sessão.

Antes de instalar qualquer plugin de qualquer fonte (inclusive este), é boa prática abrir o `SKILL.md` e ler o conteúdo — é só um arquivo de texto, então qualquer pessoa consegue conferir exatamente o que está sendo carregado no Claude.

## Atualizações

Para pegar uma versão nova depois de eu atualizar o repositório:

```
/plugin marketplace update WillenSilva/gametolls-game-dev-sincero
/plugin install game-maker-assistente
```

## Licença

MIT — livre para usar, copiar e adaptar.
