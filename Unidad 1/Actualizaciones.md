# 🖥️ Cómo comprobar y actualizar software en Linux

En Linux, saber qué software está desactualizado y actualizarlo depende del **gestor de paquetes** de tu distribución.

---

## 1️⃣ Distribuciones basadas en Debian/Ubuntu/Kali (APT)

### Comprobar actualizaciones disponibles

```bash
sudo apt update
apt list --upgradable
```
Actualizar paquetes
Actualizar solo los paquetes desactualizados:

```bash
sudo apt upgrade
```
Actualizar todos los paquetes y dependencias automáticamente:

```bash
sudo apt full-upgrade
```
Limpiar paquetes innecesarios
```bash
sudo apt autoremove
```
### 2️⃣ Distribuciones basadas en Red Hat/Fedora/CentOS (DNF o YUM)
Comprobar actualizaciones disponibles
```bash
sudo dnf check-update
# o en sistemas más antiguos:
sudo yum check-update
```
Actualizar paquetes
```bash
sudo dnf upgrade
# o con YUM:
sudo yum update
```
### 3️⃣ Distribuciones basadas en Arch Linux (Pacman)
Sincronizar repositorios
```bash
sudo pacman -Sy
```
Comprobar y actualizar paquetes
```bash
sudo pacman -Syu
```
🔹 -Syu = sincronizar repositorios y actualizar todos los paquetes.

### 4️⃣ Software instalado con Snap o Flatpak
Snap:

```bash
snap refresh --list   # ver actualizaciones disponibles
snap refresh          # actualizar todos los snaps
```
Flatpak:

```bash
flatpak update --dry-run   # ver qué se actualizaría
flatpak update             # actualizar todos los flatpaks
```
### 5️⃣ Tabla resumen de comandos

| Distribución / Gestor       | Ver actualizaciones                               | Actualizar paquetes                          |
|-----------------------------|--------------------------------------------------|---------------------------------------------|
| Debian/Ubuntu/Kali (APT)    | `sudo apt update && apt list --upgradable`       | `sudo apt upgrade` / `sudo apt full-upgrade`|
| Fedora/Red Hat/CentOS (DNF) | `sudo dnf check-update`                          | `sudo dnf upgrade`                           |
| Fedora/Red Hat/CentOS (YUM) | `sudo yum check-update`                          | `sudo yum update`                            |
| Arch Linux (Pacman)         | `sudo pacman -Sy`                                | `sudo pacman -Syu`                           |
| Snap                        | `snap refresh --list`                            | `snap refresh`                               |
| Flatpak                     | `flatpak update --dry-run`                       | `flatpak update`                             |
****

### 6️⃣ Recomendaciones generales
- Haz backup de datos importantes antes de actualizar, especialmente en servidores.
- Revisa los paquetes que se van a actualizar en sistemas críticos.
- En escritorios, puedes usar interfaces gráficas como Software Updater (Ubuntu) o Discover (KDE) para actualizar de forma visual.