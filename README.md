# LILO

An AI research assistant that runs on your own computer. You chat with it in your browser, and it reads PDFs,
runs statistics, executes code and keeps the memory of your projects.

## Install

Windows 10 or 11. Open **PowerShell**, paste this line and press Enter:

```powershell
irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 | iex
```

It downloads the installer, verifies the file and opens a window. There:

1. If any item shows up as `[XX]`, click **Instalar o que falta** (install what is missing).
2. Tick the licence and click **Instalar tudo** (install everything).
3. When the console prints **LILO PRONTO**, the chat opens in your browser.

The installer picks the models that fit your machine and shows their size before downloading. Set aside 20 to
60 GB of disk space. With an NVIDIA card, answers come much faster.

> The installer's own interface is in Portuguese. The labels you need are quoted above.

## Use

| Command | What it does |
|---|---|
| `lilo` | Starts it and opens the chat |
| `lilo status` | Shows what is running |
| `lilo down` | Shuts it down |

Full walkthrough, removal and common problems: [INSTALL.md](INSTALL.md).

## Privacy

Conversations and files stay on your computer. Nothing leaves it unless you ask an external service for
something, such as a literature search.

## Licence

**PolyForm Strict 1.0.0** ([LICENSE](LICENSE)): free, non-commercial use, with no redistribution and no
modification. For any other use, open an *issue*. Ollama, Open WebUI and Docker Desktop have their own
licences and are not redistributed here.

Required Notice: Copyright Luiz Dione Barbosa De-Melo (https://github.com/luizdione/Platform_LILO_IA)

## Help

Open an [*issue*](https://github.com/luizdione/Platform_LILO_IA/issues).
