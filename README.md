# TaskFlow

A personal, self-hosted task manager with due dates, recurrence, full-text search, a 30-day trash, reminders and a quick-capture API. It runs as a single Docker container.

I'm Portuguese and I originally built TaskFlow for my own needs, in Portuguese. I then translated the whole interface to English so that **anyone can use it**. The app is available in English and Portuguese (pt-PT); pick the language in *Settings → Preferences → Language*.

![TaskFlow tour: Today, list, board, calendar and Gantt, in light and dark themes](docs/img/tour.gif)

## How it's organised

The hierarchy is **optional at every level**; use only as much as you need:

- **Section** (e.g. Work, Home): a task can sit directly in a section.
- **Project**: a task can sit directly in a project, with no subproject.
- **Subproject**: a project has none until you create one; only then does it show up. It's there for big projects that need to be split up.

So a simple setup is just *Section › Project › Task*, and many people never need more.

## Before you install

- **The source code is closed.** I'm an inexperienced developer and the whole app was built with AI. For now I prefer not to publish the code: what I'm after is feedback from people who use it. The app is free for personal use (see [License](#license)).
- **It's a personal, single-user app.** No teams, sharing or permissions. It runs over **plain HTTP**, so use it at home, on your local network, or behind your own HTTPS.
- **It's light.** One container on SQLite, no separate database; it runs fine on a Raspberry Pi (the image is multi-architecture, amd64 and arm64).
- **It installs in two minutes**, with no configuration: one command or a `docker-compose.yml` (below).
- **There is no online demo.** The [screenshots](#screenshots) show the app with sample data.
- **Please give feedback!** Any suggestion or bug report helps, see [Feedback](#feedback).

## Screenshots

| Today | Board (dark) | Gantt |
| --- | --- | --- |
| ![Today, light theme](docs/img/desktop-today-light.png) | ![Project board, dark theme](docs/img/desktop-board-dark.png) | ![Project Gantt chart](docs/img/desktop-gantt-light.png) |

On mobile (bottom bar, sheets, and gestures that all have a tap equivalent):

<p>
  <img src="docs/img/iphone-today-light.png" alt="Today on iPhone" width="260">
  <img src="docs/img/iphone-board-dark.png" alt="Board on iPhone, dark theme" width="260">
</p>

## Language

The interface comes in **English** and **Portuguese (Portugal)**. Choose it in *Settings → Preferences → Language*; the choice applies on every device. Reminders and the morning digest follow it, and quick capture (by text or API) understands dates and recurrence in both languages ("tomorrow", "every friday"…).

![Settings → Preferences with the language selector](docs/img/settings-language-en.png)

## Install

You need [Docker](https://docs.docker.com/get-docker/).

```bash
docker run -d --name taskflow -p 127.0.0.1:3000:3000 \
  --read-only --tmpfs /tmp --cap-drop ALL \
  --security-opt no-new-privileges --pids-limit 256 \
  -v taskflow-data:/app/data --restart unless-stopped \
  ghcr.io/rj-pess/taskflow:1.2.1
```

This command already applies the hardening described in [Security](#security). Pin a version (as above) instead of `latest`, so nothing changes under you without you noticing.

Open <http://localhost:3000> and create your account (the first account is created on first visit).

> The image is multi-architecture (**amd64** and **arm64**): it runs on PCs, Raspberry Pi and Apple Silicon Macs with no extra options.

### With Docker Compose

```yaml
services:
  taskflow:
    image: ghcr.io/rj-pess/taskflow:1.2.1
    ports: ["127.0.0.1:3000:3000"]
    volumes: [taskflow-data:/app/data]
    restart: unless-stopped
    read_only: true
    tmpfs: [/tmp]
    cap_drop: [ALL]
    security_opt: ["no-new-privileges:true"]
    pids_limit: 256
volumes:
  taskflow-data:
```

## Update

```bash
docker pull ghcr.io/rj-pess/taskflow:<new-version>
docker rm -f taskflow
# run the install command again, with the new version number
```

Your data lives in the `taskflow-data` volume and survives updates.

## Backup

The app makes a daily backup (the last 14 are kept) in `data/backups/`. To copy everything to your machine:

```bash
docker cp taskflow:/app/data ./taskflow-backup
```

## Security

- The app runs over **HTTP**. Use it on a local network or behind your own HTTPS. **Do not expose it directly to the internet.**
- Every installation is yours: your data stays on your own machine.
- `127.0.0.1:3000` in the commands above makes the app reachable only from your own computer. To use it from other devices on your network, change it to `3000:3000`, and only on a network you trust.

### Trust model (please read)

The source code is closed, so **you cannot audit what the image does**, and running any image you can't inspect is a real risk. What I do to reduce it:

- The image is built by a GitHub Actions workflow, **signed with cosign** and published with a build provenance attestation and an SBOM. This proves the image came from my workflow and wasn't swapped afterwards. It does **not** prove the code is harmless, because that is something only reading the source can show.
- The container runs as a non-root user, and the commands above also make it read-only with no extra capabilities. It is isolated, but a container is not a virtual machine.
- The app has no telemetry and, apart from optional ntfy reminders that you turn on yourself, it makes no outgoing connections.

If that isn't enough for you, that is a reasonable decision. Run it on a spare machine or a VM, keep it off a network with anything sensitive, and put nothing important in it. Verify a release before running it:

```bash
cosign verify ghcr.io/rj-pess/taskflow:1.2.1 \
  --certificate-identity-regexp '^https://github.com/RJ-Pess/FlowTask/' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

(Signatures start with the first release published after this change; older versions are unsigned.)

## Feedback

- A new idea or improvement: [open a suggestion](../../issues/new?template=suggestion.yml)
- Something doesn't work: [report a bug](../../issues/new?template=bug.yml)

This is a personal project: I reply when I can, with no deadlines.

## License

Free for personal use. See [LICENSE](LICENSE).
