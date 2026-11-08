# Installing LILO

## Step by step

1. Open **PowerShell**: Start menu, type `PowerShell`, press Enter. It does not need to run as administrator.
2. Paste this line and press Enter:

   ```powershell
   irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 | iex
   ```

3. In the installer window, if any item shows up as `[XX]`, click **Instalar o que falta** (install what is
   missing) and accept each program's terms. Docker Desktop may ask you to restart Windows and to be opened
   once. Then click **Verificar** (check again).
4. Review the profile it calculated, tick the licence and click **Instalar tudo** (install everything).
5. When the console prints **LILO PRONTO**, your browser opens at http://127.0.0.1:3000.

> The installer's interface is in Portuguese. The buttons you need are quoted above.

If the console is interrupted, run the installer again: the steps already done go by quickly, and Ollama
resumes the download.

**About that line:** `irm` downloads the script and `iex` runs it. It fetches the latest release package,
verifies its hash, extracts it to `%USERPROFILE%\LILO\app` and opens the window. Programs and models are only
installed from that window, with your click.

**Rather read it first?** Download the script, open it and then run it:

```powershell
irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 -OutFile instalar.ps1
notepad instalar.ps1
powershell -ExecutionPolicy Bypass -File instalar.ps1
```

**Without that line:** download the zip from *Releases*, extract it outside any synchronised folder and
double-click `Instalar-LILO.bat`.

## Commands

In any new terminal:

| Command | What it does |
|---|---|
| `lilo` | Starts whatever is stopped and opens the chat |
| `lilo down` | Shuts it down and unloads the model from memory |
| `lilo status` | Shows what is running |
| `lilo bench` | Measures the speed on your machine |
| `lilo gpu-livre` | Releases the graphics card for another program |
| `lilo gpu-ok` | Gives the card back to LILO |
| `lilo instalar` | Reopens the installer, to change model or card |

## Requirements

- Windows 10 or 11, 64-bit.
- Miniforge, Docker Desktop with WSL 2, and Ollama: the **Instalar o que falta** button takes care of them.
- An NVIDIA card with a recent driver is optional, but without one LILO is slow.
- 20 to 60 GB of free space, depending on the models. The installer shows the total before downloading.

Docker Desktop has its own terms and requires a subscription in larger organisations. Check them as you accept.

## Updating and removing

**Updating:** run the install line again. Your settings and conversations are kept.

**Removing:**

1. `lilo down`
2. `docker compose -f "$env:USERPROFILE\LILO\app\compose.yaml" down -v` (deletes the conversations, with no
   way back)
3. `ollama rm lilo lilo-code`, and the base models listed by `ollama list`
4. `conda env remove -n lilo`
5. Delete `%USERPROFILE%\LILO`, `%LOCALAPPDATA%\LILO` and the shortcut, and remove
   `%LOCALAPPDATA%\LILO\bin` from your user PATH

## Common problems

- **`lilo` is not recognised:** open a new terminal.
- **The line does not run:** make sure you are in PowerShell, not in Command Prompt.
- **Docker is stopped:** open Docker Desktop and click **Verificar**.
- **Docker complains about WSL or virtualisation:** in PowerShell as administrator, run `wsl --install` and
  restart. If it persists, enable virtualisation in the BIOS.
- **Ollama does not answer:** open the Ollama app and click **Verificar**.
- **Slow answers:** run `lilo instalar` and pick a smaller model.
- **"GPU reservada" (card reserved):** run `lilo gpu-ok`.

**Help:** open an *issue* at https://github.com/luizdione/Platform_LILO_IA/issues. If you attach
`%LOCALAPPDATA%\LILO\instalacao.log`, check it first: it carries your user and computer names.

## Licence

**PolyForm Strict 1.0.0:** non-commercial use, with no redistribution and no modification. Full text in
`LICENSE`.
