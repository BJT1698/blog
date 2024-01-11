+++
title = "Perchè il 2024 sarà l'anno di NixOS (per me)" 
date = 2024-01-06

[taxonomies]
tags = ["nixos", "foss", "linux", "homelab"]
+++
Era da un pò che ci stavo provando, ma alla fine mi sono convinto!
<!-- more -->
> Per ora ho fatto il passaggio solo sul mio Laptop (Thinkpad T540P) ma a breve lo installerò anche sul mio homeserver.

### Che cosa mi ha convinto?

L'idea di dichiarare un intero pc in 1/2 file di configurazione è a dir poco appagante, immagina formattare il tuo pc e rimetterlo in piedi in 10 minuti scarsi configurato esattamente come prima (dati personali esclusi ovviamente), tutto questo con un file di pochi KB clonato comodamente da un repository su Github.

Lato server mi permette di dichiarare dei container direttamente da **configuration.nix** come se fossero dei docker compose (vedi [virtualisation.oci-containers](https://github.com/NixOS/nixpkgs/blob/nixos-23.11/nixos/modules/virtualisation/oci-containers.nix)), così facendo NixOS crea un servizio SystemD separato per ogni container, oltretutto a ogni rebuild della configurazione verranno scaricate eventuali nuove immagini evitando così di utilizzare servizi come Watchtower.

Vi lascio anche un piccolo snippet come esempio:

```
...
virtualisation.oci-containers = {
  backend = "docker";
  containers = {
    nginx-proxy-manager = {
      image = "jc21/nginx-proxy-manager:latest";
      ports = [
        "80:80"
        "443:443"
        "81:81"
      ];
      volumes = [
        "container_data/nginx-proxy-manager/data:/data"
        "container_data/nginx-proxy-manager/letsencrypt:/etc/letsencrypt"
      ];
    };
    homeassistant = {
      image = "ghcr.io/home-assistant/home-assistant:stable";
      ports = ["8124:8123"];
      volumes = [
        "/run/dbus:/run/dbus:ro"
        "container_data/homeassistant:/config"
      ];
      environment = {
        TZ = "Europe/Rome";
      };
      extraOptions = [
        "--privileged"
      ];
    };
  };
};
...
```


### Che cosa non mi ha convinto?

Poca roba. La wiki e la documentazione in generale fa abbastanza schifo, nulla a che vedere con la Arch Wiki che per assurdo la puoi utilizzare anche per altre distro tradizionali. Sicuramente migliorerà nel tempo.

Ogni tanto potresti beccare qualche pacchetto un po' outdated ma non è un grandissimo problema, se invece lato server lavorate principalmente con i container allora non vi tocca minimamente.
