# Pharolith MCP setup — Composer

## Instalação plug-and-play

No primeiro projeto, execute:

```bash
composer require --dev pharolith/harness
vendor/bin/pharolith setup
```

Reinicie Codex, Claude Code ou Cursor quando saída pedir. Pronto. Setup funciona
fora de projeto inicializado e prepara três clientes de uma vez.

Se preferir instalação global:

```bash
composer global require pharolith/harness
pharolith setup
```

Preview sem escrita:

```bash
vendor/bin/pharolith setup --dry-run
```

Setup autochecka bridge do package, instala cópia estável em
`~/.local/share/pharolith/bin`, configura clientes com caminho absoluto, preserva outros
servidores MCP e verifica resultado. Segunda execução é `UNCHANGED`. Package
antigo não rebaixa bridge mais recente já instalado.

Setup respeita `${CODEX_HOME}/config.toml` e
`${CLAUDE_CONFIG_DIR}/.claude.json` quando essas variáveis apontam para
diretórios absolutos. Sem overrides, usa `~/.codex/config.toml` e
`~/.claude.json`. Cursor global usa `~/.cursor/mcp.json`. BOM UTF-8, CRLF e
permissões existentes são preservados; symlinks continuam links.

Entradas criadas por versões anteriores (`pharolith-mcp-host` simples ou
wrapper npm/Composer oficial reconhecido) migram automaticamente para bridge
estável. Entrada customizada ou desativada falha fechada sem ser sobrescrita.

## Cada projeto seguinte

```bash
composer require --dev pharolith/harness
```

Não repita configuração MCP. Bridge estável executa Pharolith local de cada
projeto. Em monorepos, manifesto interno sem Pharolith próprio não mascara raiz
pai que contém instalação.

## Teste end-to-end

```bash
vendor/bin/pharolith agent-boot --json
vendor/bin/pharolith mcp probe --json
```

Depois peça ao agente no cliente para chamar `pharolith_health`. Esse tool call
é prova final de conexão na sessão atual.

## Configuração manual de emergência

Obtenha caminho estável com `vendor/bin/pharolith --json setup --dry-run` e
substitua `<BRIDGE_ABSOLUTO>` abaixo.

Codex:

```bash
codex mcp add pharolith -- <BRIDGE_ABSOLUTO>
codex mcp list
```

Claude Code:

```bash
claude mcp add --transport stdio --scope user pharolith -- <BRIDGE_ABSOLUTO>
claude mcp list
```

Cursor global usa `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "pharolith": {
      "type": "stdio",
      "command": "<BRIDGE_ABSOLUTO>",
      "args": []
    }
  }
}
```

Avançado, cliente único:

```bash
vendor/bin/pharolith mcp setup codex --yes
vendor/bin/pharolith mcp setup claude-code --yes
vendor/bin/pharolith mcp setup cursor --yes
```

Package install não edita configuração pessoal. Somente comando explícito
`pharolith setup` escreve. Sessão aberta precisa reload porque processo externo
não pode injetar nova tool nela.

Documentação oficial: [Codex MCP](https://learn.chatgpt.com/docs/extend/mcp?surface=cli),
[Claude Code MCP](https://code.claude.com/docs/en/mcp),
[Cursor MCP](https://docs.cursor.com/context/model-context-protocol).

## English

Run `composer require --dev pharolith/harness`, then
`vendor/bin/pharolith setup` once per machine. Restart changed clients. Later
projects only install package. Use `--dry-run` for preview. A nested monorepo
manifest without its own Pharolith install does not hide an installed parent
root.
