# soma-pro-releases

Este repo es el canal de actualizaciones de SOMA PRO, la app de escritorio.
Desde acá el auto-updater que viene instalado junto a la app se entera de que
hay una versión nueva y la baja.

## Cómo funciona

El repo tiene GitHub Pages activo, y Pages publica un `manifest.json` por
plataforma:

- `windows/manifest.json`
- `macos/manifest.json`

Cada manifest dice cuál es la última versión, desde dónde bajar el paquete y
qué hash tiene que dar ese paquete. Por ejemplo:

```json
{
  "version": "0.1.5",
  "url": "https://github.com/Peblo423/soma-pro-releases/releases/download/v0.1.5/soma_pro-windows-0.1.5.zip",
  "sha256": "eebd4e934edfd2ed8c8e3bd10fbfacf5f238bde97f566f79c24416bf4d15774b"
}
```

Cada vez que se abre la app (en los builds de prod), primero arranca el
updater, que lee el manifest de su plataforma y después abre la app. Si la
versión es más nueva que la instalada, baja el zip desde `url`, verifica que el
hash coincida y recién ahí lo instala. Si el hash no coincide, no instala nada
y la app instalada sigue funcionando como estaba.

## Dónde están los paquetes

Los binarios no están en el repo: están en los
[Releases](https://github.com/Peblo423/soma-pro-releases/releases). Cada
versión tiene su release `vX.Y.Z` con cuatro archivos:

- el zip de actualización de Windows, que baja el updater;
- el zip de actualización de macOS, que baja el updater;
- el instalador de Windows (`.exe`), para la primera instalación;
- el instalador de macOS (`.dmg`), para la primera instalación.

Hasta la versión 0.1.5 los zips se commiteaban al repo. Eso hacía crecer el
repo y el sitio de Pages unos 37 MB por versión, y el sitio de Pages tiene un
tope de 1 GB. Desde el 19/09/2026 los zips viven en los Releases y el repo solo
guarda los manifests. Los zips de las versiones 0.1.0 a 0.1.3 no tienen release:
solo quedan en el historial de git.

## Cómo se publica una versión

Se publica sola; este repo no se edita a mano. Los workflows
`build-windows.yml` y `build-macos.yml` del repo de la app corren en cada push a
`master`. Si la versión de `pubspec.yaml` es distinta de la que figura en el
manifest, compilan y hacen esto:

1. Crean el release de la versión, o usan el que ya creó el otro workflow.
2. Le suben el zip y el instalador de su plataforma.
3. Bajan el zip desde el release y verifican que el hash coincida.
4. Recién entonces commitean el `manifest.json` nuevo acá, y Pages lo publica.

Si algo falla antes del paso 4, el manifest no cambia y los clientes siguen en
la versión anterior.

Para publicar necesitan el secret `RELEASES_REPO_TOKEN` del repo de la app. Es
un token con permiso de escritura sobre este repo.

No commitees zips ni otros binarios acá: vuelve a crecer el repo y, con él, el
sitio de Pages.
