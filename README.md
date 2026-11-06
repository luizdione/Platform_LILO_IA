# LILO

Assistente de pesquisa com IA que roda no seu computador. Você conversa em português, no navegador, e ele lê PDFs, faz
estatística, executa código e guarda a memória dos seus projetos.

## Instalar

Windows 10 ou 11. Abra o **PowerShell**, cole esta linha e tecle Enter:

```powershell
irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 | iex
```

Ela baixa o instalador, confere o arquivo e abre uma janela. Nela:

1. Se algum item aparecer com `[XX]`, clique em **Instalar o que falta**.
2. Marque a licença e clique em **Instalar tudo**.
3. Quando o console escrever **LILO PRONTO**, o chat abre no navegador.

O instalador escolhe os modelos que cabem na sua máquina e mostra o tamanho antes de baixar. Reserve de 20 a 60 GB de
disco. Com placa NVIDIA as respostas são bem mais rápidas.

## Usar

| Comando | O que faz |
|---|---|
| `lilo` | Liga e abre o chat |
| `lilo status` | Mostra o que está ligado |
| `lilo down` | Desliga |

Passo a passo completo, remoção e problemas comuns: [INSTALACAO.md](INSTALACAO.md).

## Privacidade

Conversas e arquivos ficam no seu computador. Só sai o que você pedir a um serviço externo, como uma busca de literatura.

## Licença

**PolyForm Strict 1.0.0** ([LICENSE](LICENSE)): uso gratuito e não comercial, sem redistribuir nem modificar. Para outros
usos, abra uma *issue*. Ollama, Open WebUI e Docker Desktop têm licenças próprias e não são redistribuídos aqui.

Required Notice: Copyright Luiz Dione Barbosa De-Melo (https://github.com/luizdione/Platform_LILO_IA)

## Ajuda

Abra uma [*issue*](https://github.com/luizdione/Platform_LILO_IA/issues).
