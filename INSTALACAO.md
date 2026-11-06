# Instalar o LILO

## Passo a passo

1. Abra o **PowerShell**: menu Iniciar, digite `PowerShell`, tecle Enter. Não precisa ser como administrador.
2. Cole esta linha e tecle Enter:

   ```powershell
   irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 | iex
   ```

3. Na janela do instalador, se algum item aparecer com `[XX]`, clique em **Instalar o que falta** e aceite os termos de
   cada programa. O Docker Desktop pode pedir para reiniciar o Windows e para ser aberto uma vez. Depois clique em **Verificar**.
4. Confira o perfil calculado, marque a licença e clique em **Instalar tudo**.
5. Quando o console escrever **LILO PRONTO**, o navegador abre em http://127.0.0.1:3000.

Se o console for interrompido, rode o instalador de novo: as etapas prontas passam rápido e o Ollama retoma o download.

**Sobre a linha:** `irm` baixa o script e `iex` o executa. Ele baixa o pacote da última versão, confere o hash, extrai em
`%USERPROFILE%\LILO\app` e abre a janela. Programas e modelos só são instalados pela janela, com o seu clique.

**Prefere ler antes?** Baixe o script, abra e rode:

```powershell
irm https://github.com/luizdione/Platform_LILO_IA/releases/latest/download/instalar.ps1 -OutFile instalar.ps1
notepad instalar.ps1
powershell -ExecutionPolicy Bypass -File instalar.ps1
```

**Sem a linha:** baixe o zip em *Releases*, extraia fora de pastas sincronizadas e dê dois cliques em `Instalar-LILO.bat`.

## Comandos

Em qualquer terminal novo:

| Comando | O que faz |
|---|---|
| `lilo` | Liga o que estiver desligado e abre o chat |
| `lilo down` | Desliga e tira o modelo da memória |
| `lilo status` | Mostra o que está ligado |
| `lilo bench` | Mede a velocidade na sua máquina |
| `lilo gpu-livre` | Libera a placa para outro programa |
| `lilo gpu-ok` | Devolve a placa ao LILO |
| `lilo instalar` | Reabre o instalador, para trocar de modelo ou de placa |

## O que é preciso

- Windows 10 ou 11 de 64 bits.
- Miniforge, Docker Desktop com WSL 2 e Ollama: o botão **Instalar o que falta** cuida deles.
- Placa NVIDIA com driver recente é opcional, mas sem ela o LILO fica lento.
- De 20 a 60 GB livres, conforme os modelos. O instalador mostra o total antes de baixar.

O Docker Desktop tem termos próprios e pede assinatura em organizações maiores. Confira ao aceitar.

## Atualizar e remover

**Atualizar:** rode a linha de instalação de novo. Sua configuração e suas conversas ficam.

**Remover:**

1. `lilo down`
2. `docker compose -f "$env:USERPROFILE\LILO\app\compose.yaml" down -v` (apaga as conversas, sem volta)
3. `ollama rm lilo lilo-code`, e as bases listadas em `ollama list`
4. `conda env remove -n lilo`
5. Apague `%USERPROFILE%\LILO`, `%LOCALAPPDATA%\LILO` e o atalho, e tire `%LOCALAPPDATA%\LILO\bin` do PATH do usuário

## Problemas comuns

- **`lilo` não é reconhecido:** abra um terminal novo.
- **A linha não roda:** confira se está no PowerShell, não no Prompt de Comando.
- **Docker parado:** abra o Docker Desktop e clique em **Verificar**.
- **Docker reclama de WSL ou virtualização:** no PowerShell como administrador, rode `wsl --install` e reinicie. Se
  continuar, ligue a virtualização na BIOS.
- **Ollama não responde:** abra o app do Ollama e clique em **Verificar**.
- **Respostas lentas:** rode `lilo instalar` e escolha um modelo menor.
- **"GPU reservada":** rode `lilo gpu-ok`.

**Ajuda:** abra uma *issue* em https://github.com/luizdione/Platform_LILO_IA/issues. Se anexar
`%LOCALAPPDATA%\LILO\instalacao.log`, confira antes: ele traz o nome do seu usuário e do computador.

## Licença

**PolyForm Strict 1.0.0:** uso não comercial, sem redistribuir nem modificar. Texto completo em `LICENSE`.
