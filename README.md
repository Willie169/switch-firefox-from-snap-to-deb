## switch-firefox-from-snap-to-deb

Scripts to switch Firefox and Thunderbird from Snap to Deb version from Mozilla Team PPA, fix possible Fcitx5 not working in Firefox from PPA, remove Snap, prevent it from being installed, etc.

### Table of Contents

* [Script to Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird from Mozilla Team PPA, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended)](#script-to-switch-from-snap-firefox-and-thunderbird-to-deb-firefox-esr-and-thunderbird-from-mozilla-team-ppa-enable-unattended-upgrade-and-fix-possible-fcitx5-not-working-in-firefox-from-ppa-recommended)
* [Script to Remove Snap, Prevent it From Being Installed, Install Deb Firefox ESR and Thunderbird from Mozilla Team PPA, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended for Ubuntu with KDE Plasma)](#script-to-remove-snap-prevent-it-from-being-installed-install-deb-firefox-esr-and-thunderbird-from-mozilla-team-ppa-enable-unattended-upgrade-and-fix-possible-fcitx5-not-working-in-firefox-from-ppa-recommended-for-ubuntu-with-kde-plasma)
* [Script to Remove Snap and Prevent it From Being Installed](#script-to-remove-snap-and-prevent-it-from-being-installed)
* [Script to Switch from Snap Firefox to Deb Firefox ESR from Mozilla Team PPA](#script-to-switch-from-snap-firefox-to-deb-firefox-esr-from-mozilla-team-ppa)
* [Script to Switch from Snap Firefox to Deb Firefox from Mozilla Team PPA](#script-to-switch-from-snap-firefox-to-deb-firefox-from-mozilla-team-ppa)
* [Script to Switch from Snap Thunderbird to Deb Thunderbird from Mozilla Team PPA](#script-to-switch-from-snap-thunderbird-to-deb-thunderbird-from-mozilla-team-ppa)
* [Script to Enable Unattended Upgrade for Packages from Mozilla Team PPA](#script-to-enable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Script to Switch from Deb Firefox ESR from Mozilla Team PPA Back to Snap Firefox](#script-to-switch-from-deb-firefox-esr-from-mozilla-team-ppa-back-to-snap-firefox)
* [Script to Switch from Deb Firefox from Mozilla Team PPA Back to Snap Firefox](#script-to-switch-from-deb-firefox-from-mozilla-team-ppa-back-to-snap-firefox)
* [Script to Switch Deb Thunderbird from Mozilla Team PPA Back to Snap Thunderbird](#script-to-switch-deb-thunderbird-from-mozilla-team-ppa-back-to-snap-thunderbird)
* [Script to Disable Unattended Upgrade for Packages from Mozilla Team PPA](#script-to-disable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Remove Mozilla Team PPA](#remove-mozilla-team-ppa)
* [Script to Fix Possible Fcitx5 not Working in Firefox from PPA](#script-to-fix-possible-fcitx5-not-working-in-firefox-from-ppa)
* [References](#references)
* [My Related Repositories](#my-related-repositories)

### Script to Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird from Mozilla Team PPA, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended)

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove firefox 2>/dev/null || true
sudo apt install firefox --allow-downgrades -y
sudo apt autoremove firefox --purge -y 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y
echo 'Package: thunderbird*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: thunderbird*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/thunderbird
sudo rm -f /etc/apparmor.d/usr.bin.thunderbird
sudo rm -f /etc/apparmor.d/local/usr.bin.thunderbird
sudo systemctl stop var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo snap remove thunderbird 2>/dev/null || true
sudo apt install thunderbird --allow-downgrades -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
```

### Script to Remove Snap, Prevent it From Being Installed, Install Deb Firefox ESR and Thunderbird from Mozilla Team PPA, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended for Ubuntu with KDE Plasma)

**WARNING**: This script is safe to use on Ubuntu with KDE Plasma (such as Kubuntu). For those with GNOME software Center, Snap Store, or other built-in integrations with Snap (such as official Ubuntu from Canonical), further actions may be needed regarding them, and thus use this script only if you are not afraid of troubleshooting the system.

```
sudo systemctl stop snapd.socket
sudo systemctl stop snapd.service
sudo systemctl stop snapd.seeded.service
sudo systemctl disable snapd.socket
sudo systemctl disable snapd.service
sudo systemctl disable snapd.seeded.service
sudo apt autoremove snapd --purge -y
sudo apt autoremove plasma-discover-backend-snap -y
sudo rm -rf $HOME/snap /root/snap /snap /bin/snap /usr/bin/x11/snap /usr/bin/snap /lib/snapd /usr/lib/snapd /usr/share/snapd /usr/share/doc/snapd /var/snap /var/lib/snapd /var/cache/snapd
for bsymlink in /etc/systemd/system/default.target.wants/snap-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/default.target.wants/snapd-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/multi-user.target.wants/snap-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/multi-user.target.wants/snapd-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /var/lib/sddm/.config/systemd/user/timers.target.wants/snap.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /var/lib/sddm/.config/systemd/user/timers.target.wants/snapd.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in "$HOME"/.config/systemd/user/timers.target.wants/snap.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in "$HOME"/.config/systemd/user/timers.target.wants/snapd.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo apt install firefox --allow-downgrades -y
sudo apt autoremove firefox --purge -y 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y
echo 'Package: thunderbird*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: thunderbird*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/thunderbird
sudo rm -f /etc/apparmor.d/usr.bin.thunderbird
sudo rm -f /etc/apparmor.d/local/usr.bin.thunderbird
sudo systemctl stop var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo apt install thunderbird --allow-downgrades -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
```

### Script to Remove Snap and Prevent it From Being Installed

**WARNING**: This script is safe to use on Ubuntu with KDE Plasma (such as Kubuntu). For those with GNOME software Center, Snap Store, or other built-in integrations with Snap (such as official Ubuntu from Canonical), further actions may be needed regarding them, and thus use this script only if you are not afraid of troubleshooting the system.

```
sudo systemctl stop snapd.socket
sudo systemctl stop snapd.service
sudo systemctl stop snapd.seeded.service
sudo systemctl disable snapd.socket
sudo systemctl disable snapd.service
sudo systemctl disable snapd.seeded.service
sudo apt autoremove snapd --purge -y
sudo apt autoremove plasma-discover-backend-snap -y
sudo rm -rf $HOME/snap /root/snap /snap /bin/snap /usr/bin/x11/snap /usr/bin/snap /lib/snapd /usr/lib/snapd /usr/share/snapd /usr/share/doc/snapd /var/snap /var/lib/snapd /var/cache/snapd
for bsymlink in /etc/systemd/system/default.target.wants/snap-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/default.target.wants/snapd-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/multi-user.target.wants/snap-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /etc/systemd/system/multi-user.target.wants/snapd-* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /var/lib/sddm/.config/systemd/user/timers.target.wants/snap.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in /var/lib/sddm/.config/systemd/user/timers.target.wants/snapd.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in "$HOME"/.config/systemd/user/timers.target.wants/snap.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
for bsymlink in "$HOME"/.config/systemd/user/timers.target.wants/snapd.* ; do
  if [ -L "$bsymlink" ] && [ ! -e "$bsymlink" ]; then
    sudo rm -f "$bsymlink"
  fi
done
echo 'Package: snapd
Pin: release a=*
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/snapd
```

### Script to Switch from Snap Firefox to Deb Firefox ESR from Mozilla Team PPA

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove firefox 2>/dev/null || true
sudo apt install firefox --allow-downgrades -y
sudo apt autoremove firefox --purge -y
sudo apt install firefox-esr --allow-downgrades -y
```

### Script to Switch from Snap Firefox to Deb Firefox from Mozilla Team PPA

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove firefox 2>/dev/null || true
sudo apt install firefox --allow-downgrades -y
```

### Script to Switch from Snap Thunderbird to Deb Thunderbird from Mozilla Team PPA

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: thunderbird*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: thunderbird*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/thunderbird
sudo rm -f /etc/apparmor.d/usr.bin.thunderbird
sudo rm -f /etc/apparmor.d/local/usr.bin.thunderbird
sudo systemctl stop var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo snap remove thunderbird 2>/dev/null || true
sudo apt install thunderbird --allow-downgrades -y
```

### Script to Enable Unattended Upgrade for Packages from Mozilla Team PPA

```
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Script to Switch from Deb Firefox ESR from Mozilla Team PPA Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/firefox
sudo apt autoremove firefox-esr --purge -y
sudo snap install firefox
```

### Script to Switch from Deb Firefox from Mozilla Team PPA Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/firefox
sudo apt autoremove firefox --purge -y
sudo snap install firefox
```

### Script to Switch Deb Thunderbird from Mozilla Team PPA Back to Snap Thunderbird

```
sudo rm -rf /etc/apt/preferences.d/thunderbird
sudo apt autoremove thunderbird --purge -y
sudo snap install thunderbird
```

### Script to Disable Unattended Upgrade for Packages from Mozilla Team PPA

```
sudo rm /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Remove Mozilla Team PPA

```
sudo add-apt-repository -r ppa:mozillateam/ppa
```

### Script to Fix Possible Fcitx5 not Working in Firefox from PPA

```
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
```

### References

- Archisman Panigrahi, igi, Organic Marble, eddygeek, Yogev Neumann, & OMG Ubuntu (2024). How to install Firefox as a traditional deb package (without snap) in Ubuntu 22.04 or later versions? <https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22>
- Archisman Panigrahi & BeastOfCaerbannog (2024). How to install Thunderbird as a traditional deb package without snap in Ubuntu 24.04 and later versions? <https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2>
- Mitsuya Shibata (2023). \[ubuntu-jp:6617\] Re: PPA にある Firefox の deb パッケージでのみ fcitx5 で日本語入力ができない問題. <https://lists.ubuntu.com/archives/ubuntu-jp/2023-May/006616.html>
- Schwarzer Kater (2025). Kubuntu get rid of Snap. <https://gitlab.com/scripts94/kubuntu-get-rid-of-snap>

### My Related Repositories

* [**ubuntu-setup-with-vnc-and-gpu**](https://github.com/Willie169/ubuntu-setup-with-vnc-and-gpu)
* [**dual-boot-windows-linux-and-recovery**](https://github.com/Willie169/dual-boot-windows-linux-and-recovery)
