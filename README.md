# GameTolls Game Dev Sincero

Marketplace de skills do canal **GameTolls Game Dev Sincero** para quem cria jogos no GameMaker Studio 2 com apoio de IA (Claude Code).

## O que tem aqui

- **game-maker-assistente** — metodologia de escrita e estruturação de jogos no GameMaker (GML): arquitetura, máquina de estados, movimento/colisão, câmera, save/load, otimização e mais.

## Como instalar

Dentro do Claude Code, rode estes dois comandos (um de cada vez):

```
/plugin marketplace add WillenSilva/gametolls-game-dev-sincero
/plugin install game-maker-assistente
```

Depois disso a skill fica disponível automaticamente sempre que você pedir ajuda com arquitetura, sistemas ou código GML no GameMaker.

## Segurança

Esta skill é **apenas texto/instruções** (Markdown) que o Claude lê para saber como te ajudar:

- Não contém scripts executáveis, hooks automáticos nem MCP servers embutidos.
- Não faz chamadas de rede, não acessa arquivos fora do seu projeto e não pede nenhuma credencial, chave de API ou senha.
- Não é instalado nada no seu sistema além do arquivo `SKILL.md` em si.

Antes de instalar qualquer plugin de qualquer fonte (inclusive este), é boa prática abrir o `SKILL.md` e ler o conteúdo — é só um arquivo de texto, então qualquer pessoa consegue conferir exatamente o que está sendo carregado no Claude.

## Atualizações

Para pegar uma versão nova depois de eu atualizar o repositório:

```
/plugin marketplace update WillenSilva/gametolls-game-dev-sincero
/plugin install game-maker-assistente
```

## Licença

MIT — livre para usar, copiar e adaptar.
