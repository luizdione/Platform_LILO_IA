# LILO — assistente local de pesquisa (Ollama + Open WebUI + ponte MCP)

![Licença](https://img.shields.io/badge/licen%C3%A7a-PolyForm%20Strict%201.0.0-orange)
![Plataforma](https://img.shields.io/badge/plataforma-Windows%2010%2F11-blue)
![Uso](https://img.shields.io/badge/uso-n%C3%A3o%20comercial-lightgrey)

**LILO** (Local Intelligent LLM Orchestrator) é um assistente de pesquisa **100 % local** para quem
trabalha com bioinformática e ciência de dados: um modelo de linguagem rodando no seu próprio
computador (Ollama), uma interface de chat pronta (Open WebUI em container Docker) e uma **ponte** em
Python que dá ao modelo ferramentas de verdade — ler e buscar em PDFs, estatística, executar Python/R,
memória dos seus projetos, edição de arquivos, literatura (PubMed e afins). Nada do que você escreve
sai da sua máquina. O diálogo é em português.

Este repositório é a **página de distribuição** do LILO: aqui ficam o guia de instalação, a licença e o
pacote instalável (aba **Releases**). O código-fonte é mantido pelo autor em repositório privado.

## O que você recebe

- **Instalador que se adapta à sua máquina**: `Instalar-LILO.bat` confere os pré-requisitos, lê CPU,
  RAM (tamanho e frequência) e GPU (VRAM), deixa tudo editável, calcula o melhor perfil (modelo de chat,
  tamanho de contexto, modelos a baixar com tamanho e velocidade estimada) e aplica — do notebook sem
  placa de vídeo à estação com GPU de 24 GB.
- **Chat com ferramentas**: presets prontos na Open WebUI (LILO, Análise, Literatura, Desktop,
  Orquestrador), modo de agir manual/automático, controles de profundidade e raciocínio.
- **LILO Code** (opcional): agente de terminal (OpenCode) sobre o modelo local para tarefas de código.
- **Critério de qualidade medido**: o LILO só é considerado pronto quando "2+2" responde em menos de 1 s
  — o instalador traz o `bench` para você medir no seu hardware.

## Instalação (resumo)

Pré-requisitos já instalados: Windows 10/11 64 bits, Miniforge/conda com um ambiente Python ≥ 3.11,
Docker Desktop, Ollama; driver NVIDIA recente se houver GPU (sem GPU o LILO roda na CPU, mais devagar).

1. Baixe `LILO-<versão>-instalador.zip` (e o `.sha256` para conferir) em **Releases**.
2. Extraia numa pasta sua (por exemplo `C:\LILO`) e dê dois cliques em **`Instalar-LILO.bat`**.
3. Na janela: confira os pré-requisitos, revise o hardware detectado, clique em **Calcular perfil**,
   aceite a licença, **Aplicar configuração** e **Baixar modelos** (abre um console com `ollama pull`).
4. Abra o LILO (atalho na Área de Trabalho ou `.\lilo.ps1 up`) e acesse http://127.0.0.1:3000.

Passo a passo completo, o que é gravado onde, como o perfil é calculado (tabela por faixa de GPU) e
problemas comuns: [INSTALACAO.md](INSTALACAO.md).

## Configurar e usar

- Tudo o que o instalador decide fica em `.env` (chaves `LILO_*`) e em `%LOCALAPPDATA%\LILO\perfil\`
  (Modelfiles gerados). Mudou de placa ou quer outro modelo: rode o instalador de novo ou edite o `.env`
  e rode `.\lilo.ps1 modelos`.
- Operação diária: `.\lilo.ps1 up | down | status | warm | bench | presets | gpu-livre | gpu-ok`.
  A GPU é sua: `gpu-livre` reserva a placa para outro trabalho (dinâmica molecular, render) e o LILO
  não carrega nada até o `gpu-ok`.
- Modelos sugeridos por faixa (padrão): sem GPU → `qwen3:4b`; 8 GB → `qwen3:8b`; 12 GB →
  `qwen3:14b`; 16 GB → `qwen3:14b` a 32k; 24 GB ou mais → `qwen3:30b-a3b-instruct-2507` inteiro na
  GPU; código (LILO Code) → `qwen3-coder:30b`. Tudo é estimativa até o `bench` medir.

## Licença

**PolyForm Strict 1.0.0** ([LICENSE](LICENSE)) — licença de **uso**: você pode baixar e usar o LILO
gratuitamente para fins **não comerciais** (pesquisa, ensino, uso pessoal, instituições sem fins
lucrativos e órgãos públicos). **Não é permitido** redistribuir cópias, modificar ou criar obras
derivadas, nem usar o LILO em atividade comercial ou vendê-lo. Para qualquer outro uso, fale com o autor.
Programa de computador protegido pela Lei 9.609/98, de autor único.

Required Notice: Copyright Luiz Dione Barbosa De-Melo (https://github.com/luizdione/LILO_IA_platform)

Os componentes que o LILO usa têm as próprias licenças e não são redistribuídos aqui: Ollama (MIT),
Open WebUI (licença própria baseada na BSD-3), OpenCode (MIT), Desktop Commander (MIT), Docker MCP
Gateway (Apache 2.0) e os modelos Qwen3 e gpt-oss (Apache 2.0).

## Autor

Luiz Dione Barbosa De-Melo — desenvolvedor e único titular do LILO. Dúvidas, relatos de problema e
pedidos de uso fora da licença: abra uma *issue* neste repositório.
