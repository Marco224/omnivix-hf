# OmniVix — Hugging Face Space

Wrapper Docker minimale per deployare [OmniVix](https://github.com/enrico9034/omnivix) su Hugging Face Spaces.

> 🇬🇧 English version: [README.md](README.md)

Questo repo contiene **solo** un `Dockerfile` di una riga che pulla l'immagine multi-arch pre-buildata da GitHub Container Registry:

```dockerfile
FROM ghcr.io/enrico9034/omnivix:latest
ENV PORT=7860
EXPOSE 7860
```

Tutto il codice sorgente, il workflow di build e le release vivono nel repo principale.

## Come si usa

1. Crea uno Space su [Hugging Face](https://huggingface.co/spaces) con SDK = **Docker**.
2. Punta lo Space a questo repo (Settings → "Repository URL").
3. HF builda il `Dockerfile`, scarica l'immagine ghcr.io e la avvia sulla porta 7860.
4. Aggiungi a Stremio: `https://<tuo-space>.hf.space/manifest.json`.

## Aggiornamenti

Quando esce una nuova versione di OmniVix, l'immagine `:latest` su ghcr.io viene aggiornata automaticamente dal workflow Actions. Per forzare il pull:

- Restart manuale dello Space, oppure
- Pin a una versione specifica nel `Dockerfile` (es. `:v1.2.0` o `:sha-abc1234`).

## Self-hosting su VPS

Sono forniti due file compose. Entrambi instradano il traffico outbound attraverso WARP per bypassare i ban ASN Cloudflare 1005 sugli IP cloud (Oracle / Hetzner / OVH / …).

| File | Container | Capabilities | Quando usarlo |
|---|---|---|---|
| [`docker-compose-vps-aio.yml`](docker-compose-vps-aio.yml) | 1 (`omnivix:warp`) | nessuna | setup minimale, host con restrizioni, semplicità a singolo processo |
| [`docker-compose-vps.yml`](docker-compose-vps.yml) | 2 (`omnivix:latest` + sidecar `caomingjun/warp`) | `NET_ADMIN`, `/dev/net/tun` sul container warp | feature WARP complete (supporto UDP, `warp-cli` ufficiale); più robusto al restart di WARP |

```bash
# All-in-one (un container, nessun privilegio)
docker compose -f docker-compose-vps-aio.yml up -d

# Sidecar (due container, WARP ufficiale)
docker compose -f docker-compose-vps.yml up -d
```

Aggiungi a Stremio: `http://<tuo-host>:7000/manifest.json`.

---

## Crediti

Progetto basato su:

- [qwertyuiop8899/streamvix](https://github.com/qwertyuiop8899/streamvix)
- [qwertyuiop8899/SelfStream](https://github.com/qwertyuiop8899/SelfStream)

Grazie agli autori originali per il lavoro di reverse engineering e per aver reso il codice disponibile.
