# Instalar o LILO na sua máquina

O LILO é um assistente de pesquisa **100 % local**: um modelo de linguagem no Ollama, a interface
Open WebUI num container Docker e uma "ponte" em Python que dá ao modelo ferramentas (ler PDFs,
estatística, executar código, memória de projetos). Nada do que você escreve sai do seu computador.

O instalador **adapta o LILO ao seu hardware**: lê CPU, RAM (tamanho e frequência) e GPU (VRAM),
escolhe o modelo de chat, o tamanho de contexto e os modelos a baixar que cabem na sua placa, grava a
configuração e baixa os modelos pelo Ollama. Tudo o que ele decide fica visível e editável.

> Licença: **PolyForm Strict 1.0.0** — você pode baixar e usar o LILO para fins **não comerciais**
> (pessoais, acadêmicos, institucionais). **Não** é permitido redistribuir cópias, modificar ou usar
> comercialmente. Texto completo em `LICENSE`; programa protegido pela Lei 9.609/98.

## Pré-requisitos (já instalados por você)

| O quê | Para quê | Onde |
|---|---|---|
| Windows 10/11 64 bits | o instalador e o `lilo.ps1` são PowerShell | — |
| **Miniforge/Anaconda** com um ambiente Python ≥ 3.11 (o padrão é um env chamado `datascience`) | a ponte `lilo_host` e os scripts | https://conda-forge.org/download/ |
| **Docker Desktop** (WSL 2) | a interface Open WebUI | https://www.docker.com/products/docker-desktop/ |
| **Ollama** | o motor dos modelos | https://ollama.com/download |
| Driver NVIDIA recente (`nvidia-smi` funcionando) | GPU; sem placa o LILO roda na CPU (lento) | https://www.nvidia.com/drivers |
| git (opcional) | ferramenta `executar_git` | https://git-scm.com |
| Node.js/npm + `opencode-ai@1.19.5` (opcional) | **LILO Code**, o agente de terminal | `npm install -g opencode-ai@1.19.5` |

Espaço em disco: 10–50 GB para os modelos (o instalador mostra o total antes de baixar).

## Passo a passo

1. Baixe o pacote `LILO-<versão>-instalador.zip` (e o `.sha256`, se quiser conferir) na página de *Releases* de
   https://github.com/luizdione/LILO_IA_platform e extraia numa pasta sua, por exemplo
   `C:\LILO` (evite pastas sincronizadas pelo OneDrive para os modelos e para a pasta de trabalho).
2. Dê dois cliques em **`Instalar-LILO.bat`** (ou rode `.\lilo.ps1 instalar`). Abre uma janela com
   cinco blocos:
   1. **Pré-requisitos** — `[OK]`, `[!!]` (opcional/avisos) ou `[XX]` (falta). Se o Python não
      for encontrado, aponte o `python.exe` do seu env com o botão `...`.
   2. **Seu hardware** — CPU, núcleos, threads, MHz; RAM em GB e MHz; GPU e VRAM em MiB; disco livre.
      Tudo detectado automaticamente e **editável** (corrija se souber melhor; `VRAM = 0` significa
      "sem placa dedicada").
   3. **Pastas** — *Trabalho* (onde o LILO cria arquivos e executa código), *Projetos* (pastas com
      `.project-memory` que ele pode ler e editar) e *PDFs* (opcional: biblioteca a indexar).
   4. **Calcular perfil** — mostra o perfil (`gpu-12gb`, `cpu`, ...), o modelo de chat e o de código
      com contexto, onde rodam (inteiro na GPU, híbrido GPU+RAM ou CPU), tokens/s estimados e a lista
      de modelos a baixar com tamanho (os marcados são os recomendados).
   5. **Aplicar e instalar** — marque o aceite da licença e clique em **Aplicar configuração**
      (grava `.env`, Modelfiles e `opencode.json`), depois em **Baixar modelos** (abre um console com
      `ollama pull` dos marcados e `ollama create lilo`). *Instalar dependências Python* roda
      `pip install -r requirements.txt` no seu env; *Abrir o LILO* roda `lilo.ps1 up`.
3. Quando o console dos downloads terminar, abra o LILO (atalho na Área de Trabalho ou
   `.\lilo.ps1 up`) e acesse http://127.0.0.1:3000.

Sem janela (servidor, automação): `.\lilo.ps1 perfil` mostra o perfil detectado;
`.\lilo.ps1 perfil aplicar [--vram 24576 --ram-gb 64 ...]` grava tudo; `.\lilo.ps1 modelos` faz o
`ollama create`; os `ollama pull` você roda à mão com as tags que o perfil listou.

## O que o instalador grava (e nada mais)

| Onde | O quê |
|---|---|
| `<pasta do LILO>\.env` | `LILO_PERFIL`, `LILO_MODELO_BASE`, `LILO_NUM_CTX`, `LILO_MODELO_CODE_BASE`, `LILO_NUM_CTX_CODE`, `LILO_VRAM_MINIMA`, `LILO_COMPACTACAO`, `LILO_PY`, `LILO_HOST_TRABALHO`, `LILO_HOST_RAIZES`, `LILO_PROJETOS_RAIZES`, `LILO_BIBLIOTECA_PDFS`. O arquivo nunca é mostrado nem versionado. |
| `%LOCALAPPDATA%\LILO\perfil\` | `Modelfile.lilo` e `Modelfile.lilo-code` (os do repositório com `FROM` e `num_ctx` trocados), `opencode.json` (com `limit.context` igual ao `num_ctx`), `perfil.json` (tudo o que foi calculado) e o script `baixar_modelos.ps1` que o console executa. |
| Variáveis do usuário (se marcado) | `OLLAMA_FLASH_ATTENTION=1`, `OLLAMA_KV_CACHE_TYPE=q8_0`, `OLLAMA_KEEP_ALIVE=8h` — contexto maior na mesma VRAM e modelo residente entre perguntas. Reinicie o Ollama depois. |
| Atalhos (se marcado) | `LILO.lnk` na Área de Trabalho e, opcionalmente, na Inicialização (`scripts\criar_atalho.py`). |

O `lilo.ps1` lê o `.env` ao iniciar: a VRAM mínima, o modelo e as pastas passam a ser os do perfil,
e os Modelfiles gerados têm precedência sobre os do repositório (`ollama\Modelfile.*`).

## Como o perfil é calculado

`scripts\perfil_hardware.py` (só biblioteca padrão; testado em `tests\test_perfil_hardware.py`):

- **Residente na GPU** = pesos do modelo + cache KV (`q8_0`, KB/token × contexto) + buffers.
  "Cabe inteiro" quando sobra 700 MiB para o driver e 1 GiB de margem. Se não cabe, o Ollama divide
  o modelo com a RAM (híbrido) — funciona, mas a primeira resposta demora mais.
- **tok/s estimado** = 1 / (bytes ativos por token ÷ largura de banda + 3 ms). GPU: banda da placa
  (tabela por nome; por tamanho se desconhecida). RAM: canais × 8 B × MT/s × 0,7 — por isso a
  **frequência da RAM** importa quando o modelo não cabe na placa. Calibrado na RTX 3060 12 GB:
  qwen3:14b 34 tok/s (medido 33,8), MoE 30B-A3B híbrido 26 (medido 27).
- **Escolha**: o melhor modelo da família que cabe inteiro na GPU com pelo menos 8k de contexto; o
  maior contexto possível até 32k (64k a partir de 30 GB de VRAM). Para código (LILO Code, sempre
  32k): o coder MoE se couber inteiro ou híbrido rápido com RAM ≥ 32 GB; senão um coder denso menor.

| Hardware | Chat (`lilo`) | Código (`lilo-code`) |
|---|---|---|
| sem GPU, RAM < 32 GB | qwen3:4b (ou 1.7b) na CPU, 8k | — |
| sem GPU, RAM ≥ 32 GB | qwen3:30b-a3b-instruct-2507 na CPU, 16k (~15 tok/s em DDR4) | qwen3-coder:30b (CPU) |
| GPU 4–7 GB | qwen3:4b, 16k | — |
| GPU 8–11 GB | qwen3:8b, 8k | qwen3-coder:30b híbrido (se RAM ≥ 32 GB) |
| GPU 12–15 GB (RTX 3060) | **qwen3:14b, 8k** (configuração medida do autor) | qwen3-coder:30b híbrido |
| GPU 16–23 GB | qwen3:14b, 32k | qwen3-coder:30b híbrido ou qwen2.5-coder:14b |
| GPU 24–31 GB | qwen3:30b-a3b-instruct-2507, 32k, inteiro na GPU | qwen3-coder:30b inteiro |
| GPU ≥ 32 GB | idem, 64k | qwen3-coder:30b (+ gpt-oss:20b opcional) |

Tudo é estimativa: depois de instalar, `.\lilo.ps1 bench` mede de verdade (meta: "2+2" responde em
menos de 1 s). Se o bench reprovar, reduza o contexto ou escolha um modelo menor no `.env` e rode
`.\lilo.ps1 modelos`.

## Ajustar depois

- Mudou de placa ou quer outro modelo: rode o instalador de novo, ou edite as linhas `LILO_*` do
  `.env` e rode `.\lilo.ps1 modelos`.
- `.\lilo.ps1 status` mostra o perfil em uso, os modelos carregados e a VRAM.
- Para recomendar um modelo que não está no catálogo, acrescente-o em `CATALOGO` no
  `scripts\perfil_hardware.py` (tamanho em GB e KB de KV por token).

## Problemas comuns

- **"A execução de scripts foi desabilitada"**: o `.bat` já passa `-ExecutionPolicy Bypass`; se
  rodar o `.ps1` à mão, use `powershell -STA -ExecutionPolicy Bypass -File instalador\Instalar-LILO.ps1`.
- **Docker `[!!] motor não está rodando`**: abra o Docker Desktop e clique em *Verificar*.
- **Ollama não responde em 11434**: abra o app do Ollama (ícone na bandeja) e clique em *Verificar*.
- **GPU AMD/Intel**: o Ollama no Windows dá suporte parcial (AMD RX 6800+/7000 via ROCm; Intel Arc
  experimental). O instalador avisa; confira com `ollama ps` se o modelo está na GPU.
- **Modelo lento ou "truncating input prompt" no log do Ollama**: o contexto escolhido não coube;
  diminua `LILO_NUM_CTX` no `.env`, rode `.\lilo.ps1 modelos` e meça com `.\lilo.ps1 bench`.
- **Interruptor de GPU em OFF** (`~\.ollama_delegate\switch.json`): o LILO não carrega modelo
  enquanto a GPU estiver reservada para outro trabalho; `.\lilo.ps1 gpu-ok` devolve a placa.
