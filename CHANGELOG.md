# Changelog

## Fork changes — Antoine Valentin

> This section documents changes specific to the fork
> [antoinevalentinHA/ha-linky](https://github.com/antoinevalentinHA/ha-linky).
> These are **not** official releases of the upstream project
> [bokub/ha-linky](https://github.com/bokub/ha-linky). Only the Recorder
> metadata fix (section A) is proposed upstream; everything else is fork-only.

### 2026-07-02 — Local fork for Home Assistant Recorder metadata fix

#### A. Upstream candidate fix

- Added metadata fields to the `recorder/import_statistics` WebSocket call:
  - `mean_type: 0` (no averaging — Linky statistics are cumulative sums).
  - `unit_class: 'energy'` for Wh-based energy statistics.
  - `unit_class: null` for cost statistics (€, no Home Assistant unit class).
- Kept `has_mean: false` for backward compatibility with older Home Assistant versions.
- Fixes the daily Home Assistant Recorder deprecation warnings:
  - `import_statistics` called without `mean_type` in metadata.
  - `import_statistics` called without `unit_class` in metadata.
  - (Both are announced to stop working in HA Core 2026.11.)
- Proposed upstream via PR [#191](https://github.com/bokub/ha-linky/pull/191);
  related upstream issues [#161](https://github.com/bokub/ha-linky/issues/161)
  and [#182](https://github.com/bokub/ha-linky/issues/182).

#### B. Fork-only Home Assistant add-on adaptations

_Not intended for upstream PR #191._

- `repository.yaml`: repository display name set to "Linky - Antoine fork",
  `url` and `maintainer` point to the fork.
- `config.yaml`: add-on display `name` set to "Linky - Antoine fork" and `url`
  points to the fork.
- `slug` kept as `linky` (unchanged add-on identity).
- `version` kept at `1.7.0` intentionally (a `-fork` prerelease suffix would
  sort lower than `1.7.0` and would not be offered as an update).
- Removed the upstream prebuilt image reference
  (`image: ghcr.io/bokub/ha-linky-{arch}`) from `config.yaml` so the Supervisor
  builds the add-on locally from the fork instead of pulling the upstream image.
- Added `build.yaml` providing `build_from` base images per architecture, so the
  Dockerfile's `BUILD_FROM` argument is populated during local Supervisor builds.
- `Dockerfile`: `org.opencontainers.image.source` OCI label points to the fork.

#### C. Runtime validation

- Local Supervisor build succeeded after `build.yaml` was added (fixes the
  `base name ($BUILD_FROM) should not be blank` error).
- Add-on started successfully after re-entering the PRM/token configuration
  (add-on options are reset on reinstall; at least one meter needs both `prm`
  and `token`).
- Synchronization connected to Home Assistant and reported data up-to-date.
- Final confirmation pending the next scheduled imports (06:xx / 09:xx) to
  verify the Recorder deprecation warnings no longer appear.

#### D. Notes / rollback

- The fork-only metadata and build changes (section B) are deliberately kept out
  of upstream PR #191, which stays limited to the Recorder metadata fix.
- The branch `fix/recorder-statistics-metadata-ha-2026-11` must be kept until
  PR #191 is merged or closed.
- To return to the upstream prebuilt-image strategy, revert the fork-only
  commits — but that would stop running the local Recorder fix until upstream
  merges it and publishes a new image.

---

## 1.7.0 - 7 janvier 2026

- Possibilité de calculer les coûts en se basant sur une entité Home Assistant contenant le prix de l'énergie (ex : `sensor.electricity_price`) - merci à [@vinzd](https://github.com/vinzd) !

## 1.6.1 - 28 octobre 2025

- Ajout de logs pour mieux comprendre les problèmes de calcul des coûts

## 1.6.0 - 15 octobre 2025

- Ajout du calcul des coûts pour les PRM en production
- Prise en compte des demi-heures dans le calcul des coûts
- Modification du format du fichier CSV pour l'import d'historique (compatible avec [Conso Downloader](https://github.com/bokub/conso-downloader/tree/master?tab=readme-ov-file#conso-downloader))

## 1.5.0 - 23 juillet 2024

- Ajout du calcul des coûts

## 1.4.0 - 27 février 2024

- Ajout de l'import de données historiques via un fichier CSV

## 1.3.1 - 25 février 2024

- Correction de la synchronisation quotidienne qui ne se lançait pas correctement

## 1.3.0 - 22 février 2024

- Nouveau format de configuration
  - **N.B**: Votre ancienne migration sera **automatiquement** convertie au nouveau format au premier démarrage de l'add-on. Vous devrez peut-être rafraîchir la page pour voir les changements apparaître dans l'onglet configuration.
- Ajout de la possibilité de gérer plusieurs compteurs (#22)
- Correctif d'un bug qui survenait lors d'un trou de données de 7 jours exactement (#16)

## 1.2.0 - 15 novembre 2023

- Ajout de la synchronisation de la production d'énergie - merci à [@cddu33](https://github.com/cddu33) pour son aide !
