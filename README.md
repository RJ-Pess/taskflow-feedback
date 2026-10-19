# TaskFlow

> **English:** a personal, self-hosted task manager. Sections › Projects › Subprojects, due dates, recurrence, full-text search, 30-day trash, ntfy reminders and a quick-capture API. Runs as a single Docker container (SQLite, `linux/amd64` and `linux/arm64`, so Raspberry Pi and Apple Silicon work). **The interface is available in English and Portuguese (pt-PT); switch in Settings → Preferences → Language.** **The source code is closed. I'm an inexperienced developer, the whole app was built with AI, and I'm looking for feedback.** It is free for personal use; this repository is for installation and feedback. Install: see [Instalar](#instalar) (one `docker run`).

Gestor de tarefas pessoal, em português **e inglês** (escolhe em Definições → Preferências → Idioma): **Secções › Projetos › Subprojetos**, prazos, recorrência, pesquisa, lixo com 30 dias, lembretes e captura rápida por API.

![Tour pela TaskFlow: Hoje, lista, board, calendário e Gantt, em tema claro e escuro](docs/img/tour.gif)

## Antes de instalares

- **O código é fechado.** Sou uma pessoa inexperiente e esta aplicação foi feita **inteiramente com IA**. Por agora prefiro não a publicar: o que procuro é feedback de quem a usa. A app é grátis para uso pessoal (ver [Licença](#licença)).
- **É uma app pessoal, para uma só pessoa.** Não tem equipas, partilha nem permissões, e corre em **HTTP** (sem HTTPS). Usa-a em casa, na rede local ou atrás do teu próprio HTTPS.
- **Instala-se em dois minutos**, sem configuração: um comando ou um `docker-compose.yml` (abaixo).
- **Sem demo online.** As [capturas](#capturas) mostram a app com dados de exemplo.
- **Dá feedback!** Qualquer sugestão ou bug ajuda, ver [Sugestões e problemas](#sugestões-e-problemas).

## Capturas

| Hoje | Board (escuro) | Gantt |
| --- | --- | --- |
| ![Hoje, tema claro](docs/img/desktop-hoje-claro.png) | ![Board de projeto, tema escuro](docs/img/desktop-board-escuro.png) | ![Gantt de projeto](docs/img/desktop-gantt-claro.png) |

No telemóvel (barra inferior, folhas e gestos com equivalente por toque):

<p>
  <img src="docs/img/iphone-hoje-claro.png" alt="Hoje no iPhone" width="260">
  <img src="docs/img/iphone-board-escuro.png" alt="Board no iPhone, tema escuro" width="260">
</p>

## Idioma / Language

A interface está em **português (Portugal)** e **inglês**. A escolha faz-se em *Definições → Preferências → Idioma* e vale em todos os dispositivos. Os lembretes e o resumo da manhã seguem o idioma escolhido, e a captura rápida (por texto ou API) entende datas e recorrência nos dois idiomas («amanhã», «tomorrow», «todas as sextas», «every friday»…).

The interface comes in **English** and **Portuguese (Portugal)**. Pick it in *Settings → Preferences → Language*; it applies on every device. Reminders and the morning digest follow it, and quick capture understands dates and recurrence in both languages.

![Settings → Preferences with the language selector, in English](docs/img/settings-language-en.png)

## Instalar

Precisas de [Docker](https://docs.docker.com/get-docker/).

```bash
docker run -d --name taskflow -p 3000:3000 \
  -v taskflow-data:/app/data --restart unless-stopped \
  ghcr.io/rj-pess/taskflow:latest
```

Abre <http://localhost:3000> e cria a tua conta (a primeira conta é criada no primeiro acesso).

> A imagem é multi-arquitetura (**amd64** e **arm64**): funciona em PCs, Raspberry Pi e Macs com Apple Silicon, sem opções extra.

### Com Docker Compose

```yaml
services:
  taskflow:
    image: ghcr.io/rj-pess/taskflow:latest
    ports: ["3000:3000"]
    volumes: [taskflow-data:/app/data]
    restart: unless-stopped
volumes:
  taskflow-data:
```

## Atualizar

```bash
docker pull ghcr.io/rj-pess/taskflow:latest
docker rm -f taskflow
# volta a correr o comando de instalação
```

Os dados ficam no volume `taskflow-data` e sobrevivem à atualização.

## Backup

A app faz um backup diário (últimos 14) em `data/backups/`. Para copiares tudo para a tua máquina:

```bash
docker cp taskflow:/app/data ./taskflow-backup
```

## Segurança

- A app corre em **HTTP**. Usa-a em rede local ou atrás do teu próprio HTTPS. **Não a expões diretamente à internet.**
- Cada instalação é tua: os teus dados ficam só na tua máquina.

## Sugestões e problemas

- Ideia nova ou melhoria: [abrir sugestão](../../issues/new?template=sugestao.yml)
- Algo não funciona: [reportar bug](../../issues/new?template=bug.yml)

É um projeto pessoal: respondo quando puder, sem prazos.

## Licença

Uso pessoal e gratuito. Ver [LICENSE](LICENSE).
