# Script d'extras de Waydroid

Script pa añadir Google Apps, microG, Magisk, traducción ARM, Widevine y otros extras a Waydroid.

Esti fork (`BENDER35/waydroid_script_2026`) caltién el fluxu orixinal de `casualsnek/waydroid_script` y corrixe dellos problemes atopaos al probalo en sistemes actuales.

## Característiques

- Instalar / desinstalar OpenGApps (Android 11) o MindTheGapps (Android 13)
- Instalar / desinstalar microG (Standard, NoGoolag, UNLP, Minimal, MinimalIAP)
- Instalar / desinstalar la traducción ARM libndk (recomendada pa CPU AMD)
- Instalar / desinstalar la traducción ARM libhoudini (recomendada pa CPU Intel)
- Instalar / desinstalar Magisk Delta
- Instalar / desinstalar Widevine DRM L3
- Instalar / desinstalar Smart Dock
- Instalar / desinstalar F-Droid Privileged Extension
- Instalar un certificáu CA autofirmáu nel almacén de confianza del sistema (`mitm`)
- Hacks: `nodataperm`, `hidestatusbar`
- Llograr l'Android ID necesariu pa certificar un dispositivu con Google

## Requisitos

- Waydroid inicializáu y funcionando
- Python 3
- `lzip`
- Paquetes de Python: `requests`, `tqdm`, `InquirerPy`

En Debian / Ubuntu:

```bash
sudo apt install lzip python3 python3-pip
```

## Requisitu de Wayland (importante)

Waydroid **namás funciona nuna sesión Wayland**. Enantes d'executar esti script, asegúrate de que'l to escritoriu ta en Wayland:

```bash
echo $XDG_SESSION_TYPE
```

Tien de mostrar `wayland`. Si muestra `x11`, tienes dos opciones:

1. **Cambiar a una sesión Wayland** (recomendáu):
   - Cierra sesión, abre'l menú de sesión del xestor de pantalla (GDM/SDDM/LightDM) y escueye una sesión Wayland como *Ubuntu on Wayland*, *GNOME on Wayland* o *KDE Plasma (Wayland)*.
   - N'Ubuntu/GNOME, Wayland ye'l predetermináu dende 22.04. Si taba desactiváu, reactívalu en `/etc/gdm3/custom.conf` (`WaylandEnable=true`) y reinicia GDM.
   - En Xubuntu/XFCE 26.04 (beta, soporte esperimental de Wayland), prueba `startxfce4 --wayland` o la sesión *Xfce (Wayland)*. Si nun ta disponible, usa un compositor anidáu (mira embaxo).
   - En Fedora, Wayland ye'l predetermináu pa GNOME/KDE.
   - En Arch/Manjaro, instala un compositor/escritoriu Wayland (GNOME, KDE Plasma, Sway, Hyprland) o usa `cage`/`weston`.
2. **Usar un compositor Wayland anidáu nun escritoriu X11**:
   ```bash
   sudo apt install cage weston
   weston --xwayland
   # or
   cage -- waydroid show-full-ui
   ```

Al executar Waydroid o esti script con `sudo`, caltién les variables d'enteorno de Wayland pa que'l socket de sesión siga siendo visible:

```bash
sudo -E python3 main.py
```

### Nota sobre XWayland

XWayland permite executar **aplicaciones X11** dientro d'una sesión Wayland. **Non** ye un sustitutu d'un compositor Wayland y por sigo mesmu nun permite executar Waydroid. Waydroid sigue precisando un compositor Wayland (GNOME/KDE/Sway/Hyprland, o `weston`/`cage`).

Si precisas XWayland (por exemplu pa apps antigües namás con X11), instálalu:

- Debian/Ubuntu: `sudo apt install xwayland`
- Fedora: `sudo dnf install xorg-x11-server-Xwayland`
- Arch/Manjaro: `sudo pacman -S xorg-xwayland`

Nuna sesión Wayland normal, XWayland aníciase automáticamente al llanzar una app X11. Puedes comprobalo con:

```bash
echo $DISPLAY
xlsclients
```

Si quies anicialo manualmente:

```bash
Xwayland :1 &
DISPLAY=:1 <application>
```

### Notes por distribución

- **Ubuntu / Debian y derivaes**: ríquese sesión Wayland (Ubuntu 22.04+). Instala Waydroid dende'l repositoriu oficial o'l repositoriu de Waydroid según la documentación de Waydroid.
- **Xubuntu 26.04 (beta)**: el soporte de Wayland en XFCE ye esperimental; si la sesión nun ta disponible, instala `weston` y/o `cage` y executa Waydroid al traviés d'ellos.
- **Fedora / openSUSE / Arch / Void / NixOS**: usa la sesión Wayland apurrida pol to escritoriu/compositor; los paquetes de Waydroid tán disponibles nos repositorios respectivos.

## Instalar Waydroid (ayudante opcional)

Esti repositoriu tamién inclúi `install_waydroid.sh`, un ayudante pa instalar Waydroid en distribuciones habituales:

```bash
sudo ./install_waydroid.sh            # vanilla
sudo ./install_waydroid.sh --gapps    # with Google Apps
sudo ./install_waydroid.sh --nested   # also install weston/cage for X11 sessions
```

## Instalación

```bash
git clone https://github.com/BENDER35/waydroid_script_2026.git
cd waydroid_script_2026
python3 -m venv venv
venv/bin/pip install -r requirements.txt
```

## Usu

### Modo interactivu

```bash
sudo venv/bin/python3 main.py
```

### Llinia de comandos

```bash
# Get the Android ID for Play Store certification
sudo venv/bin/python3 main.py certified

# Install one or more extras
sudo venv/bin/python3 main.py install gapps
sudo venv/bin/python3 main.py install microg magisk libndk
sudo venv/bin/python3 main.py install mitm --ca-cert mycert.pem
sudo venv/bin/python3 main.py install fdroidpriv

# Remove one or more extras
sudo venv/bin/python3 main.py remove gapps
sudo venv/bin/python3 main.py uninstall microg magisk

# Apply hacks (Android 11; nodataperm is also available on Android 13)
sudo venv/bin/python3 main.py hack nodataperm
sudo venv/bin/python3 main.py hack hidestatusbar
```

La versión d'Android puede escoyese con `-a` / `--android-version` (por defectu: 13):

```bash
sudo venv/bin/python3 main.py -a 11 install gapps
```

## Dependencies

`lzip` ye necesariu pa estrayer OpenGApps. Instálalu col xestor de paquetes de la to distribución:

- Arch / Manjaro: `sudo pacman -S lzip`
- Debian / Ubuntu: `sudo apt install lzip`
- Fedora / RHEL: `sudo dnf install lzip`
- openSUSE: `sudo zypper install lzip`

## Notes

- El script tien d'executase como root.
- En configuraciones ensin overlayfs el script monta les imáxenes de Waydroid en `/tmp/waydroid`; asegúrate de que Waydroid ta deteníu enantes d'executalo.
- Magisk s'instala nel próximu arranque.
- Pa la certificación de Google Play, executa `certified`, rexistra'l ID impresu en https://google.com/android/uncertified/, espera 10-20 minutos y depués llimpia la caché de Google Play Services.

## Solución de problemes

- `ModuleNotFoundError: No module named 'InquirerPy'`: instala los requisitos dientro del entorno virtual (`venv/bin/pip install -r requirements.txt`).
- Waydroid tien de tar en funcionamientu pa `certified`.
- Si un hack rompe l'arranque, quítalu con `sudo venv/bin/python3 main.py remove <hack>`.

## Créditos

- [WayDroid](https://github.com/waydroid/waydroid)
- [casualsnek/waydroid_script](https://github.com/casualsnek/waydroid_script)
- [Magisk Delta](https://huskydg.github.io/magisk-files/)
- [microG Project](https://microg.org)
- [Open GApps](https://opengapps.org)
- [Smart Dock](https://github.com/axel358/smartdock)
- [wd-scripts](https://github.com/electrikjesus/wd-scripts/)
