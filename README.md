## switch-firefox-from-snap-to-deb

Scripts to switch Firefox and Thunderbird from Snap to Deb version from Mozilla Team PPA, remove Snap and prevent it from being installed, install Deb Chromium from XtraDeb PPA and prevent Snap Chromium from being installed, and install Google Chrome from Google Chrome PPA.

These scripts also solves potential issues of Fcitx5 on Firefox or Firefox ESR from Mozilla Team PPA. For Firefox and Thunderbird, either [Switch from Snap Firefox and Thunderbird to Deb Firefox and Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-and-thunderbird-to-deb-firefox-and-thunderbird-from-mozilla-team-ppa-and-enable-unattended-upgrade) or [Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-and-thunderbird-to-deb-firefox-esr-and-thunderbird-from-mozilla-team-ppa-and-enable-unattended-upgrade) is recommended. For Linux Mint user, it is recommended to install from  their [official repository](http://packages.linuxmint.com) instead of using scripts here.

### Table of Contents

* [After Switching](#after-switching)
* [Switch from Snap Firefox and Thunderbird to Deb Firefox and Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-and-thunderbird-to-deb-firefox-and-thunderbird-from-mozilla-team-ppa-and-enable-unattended-upgrade)
* [Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-and-thunderbird-to-deb-firefox-esr-and-thunderbird-from-mozilla-team-ppa-and-enable-unattended-upgrade)
* [Install Deb Chromium from XtraDeb PPA and Prevent Snap Chromium From Being Installed](#install-deb-chromium-from-xtradeb-ppa-and-prevent-snap-chromium-from-being-installed)
* [Install Deb Google Chrome from Google Chrome Stable PPA](#install-deb-google-chrome-from-google-chrome-ppa)
* [Remove Snap and Prevent it From Being Installed for Ubuntu with KDE Plasma](#remove-snap-and-prevent-it-from-being-installed-for-ubuntu-with-kde-plasma)
* [Switch from Snap Firefox to Deb Firefox from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-to-deb-firefox-from-mozilla-team-ppa-and Enable Unattended Upgrade)
* [Switch from Snap Firefox to Deb Firefox ESR from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-firefox-to-deb-firefox-esr-from-mozilla-team-ppa-and Enable Unattended Upgrade)
* [Switch from Snap Thunderbird to Deb Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade](#switch-from-snap-thunderbird-to-deb-thunderbird-from-mozilla-team-ppa-and-enable-unattended-upgrade)
* [Enable Unattended Upgrade for Packages from Mozilla Team PPA](#enable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Switch from Deb Firefox from Mozilla Team PPA Back to Snap Firefox](#switch-from-deb-firefox-from-mozilla-team-ppa-back-to-snap-firefox)
* [Switch from Deb Firefox ESR from Mozilla Team PPA Back to Snap Firefox](#switch-from-deb-firefox-esr-from-mozilla-team-ppa-back-to-snap-firefox)
* [Switch Deb Thunderbird from Mozilla Team PPA Back to Snap Thunderbird](#switch-deb-thunderbird-from-mozilla-team-ppa-back-to-snap-thunderbird)
* [Disable Unattended Upgrade for Packages from Mozilla Team PPA](#disable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Remove Mozilla Team PPA](#remove-mozilla-team-ppa)
* [Switch Deb Chromium from XtraDeb PPA Back to Snap Chromium](#switch-deb-chromium-from-xtradeb-ppa-back-to-snap-chromium)
* [Remove XtraDeb PPA](#remove-xtradeb-ppa)
* [References](#references)
* [My Related Repositories](#my-related-repositories)

### After Switching

You may want to re-configure launchers in your Desktop Environment.

If any error occurs when opening Firefox, try running:
```
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
```
to disable Firefox AppArmor profile.

### Switch from Snap Firefox and Thunderbird to Deb Firefox and Thunderbird from Mozilla Team PPA and Enable Unattended Upgrade

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
sudo apt install firefox --allow-downgrades -y </dev/null
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
sudo rm /var/lib/snapd/desktop/applications/firefox*.desktop 2>/dev/null || true
sudo rm /var/lib/snapd/inhibit/firefox.lock 2>/dev/null || true
rm -r snap/firefox 2>/dev/null || true
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
sudo apt install thunderbird --allow-downgrades -y </dev/null
sudo rm /var/lib/snapd/desktop/applications/thunderbird*.desktop 2>/dev/null || true
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird from Mozilla Team PPA, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA

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
sudo apt install firefox --allow-downgrades -y </dev/null
sudo apt autoremove firefox --purge -y  </dev/null 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y </dev/null
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
sudo rm /var/lib/snapd/desktop/applications/firefox*.desktop 2>/dev/null || true
sudo rm /var/lib/snapd/inhibit/firefox.lock 2>/dev/null || true
rm -r snap/firefox 2>/dev/null || true
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
sudo apt install thunderbird --allow-downgrades -y </dev/null
sudo rm /var/lib/snapd/desktop/applications/thunderbird*.desktop 2>/dev/null || true
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Install Deb Chromium from XtraDeb PPA and Prevent Snap Chromium From Being Installed

```
sudo add-apt-repository ppa:xtradeb/apps -y
echo 'Package: chromium*
Pin: release o=LP-PPA-xtradeb-apps
Pin-Priority: 1001

Package: chromium*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/chromium
sudo apt update
sudo apt install chromium -y </dev/null
```
You may also want to install other packages from XtraDeb PPA, such as `chromium-driver` and `chromium-l10n`.

### Install Deb Google Chrome from Google Chrome PPA
```
sudo mkdir -p /etc/apt/keyrings
wget -qO - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo tee /etc/apt/keyrings/google.asc >/dev/null
sudo sh -c 'echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/google.asc] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google.list'
sudo apt update
```
For Google Chrome Stable:
```
sudo apt install google-chrome-stable -y </dev/null
```
For Google Chrome Beta:
```
sudo apt install google-chrome-beta -y </dev/null
```
For Google Chrome Unstable:
```
sudo apt install google-chrome-unstable -y </dev/null
```
Remove Google Chrome PPA with:
```
sudo rm -f /etc/apt/sources.list.d/google.list
sudo rm -f /etc/apt/keyrings/google.asc
```

### Remove Snap and Prevent it From Being Installed for Ubuntu with KDE Plasma

**WARNING**: This script should be safe to use on Ubuntu with KDE Plasma, such as Kubuntu. For those with GNOME software Center, Snap Store, or other built-in integrations with Snap, such as official Ubuntu from Canonical, further actions are needed, and use this script only if you are willing to troubleshoot the system.
```
sudo systemctl stop snapd.socket
sudo systemctl stop snapd.service
sudo systemctl stop snapd.seeded.service
sudo systemctl disable snapd.socket
sudo systemctl disable snapd.service
sudo systemctl disable snapd.seeded.service
sudo apt autoremove snapd --purge -y </dev/null
sudo apt autoremove plasma-discover-backend-snap -y </dev/null
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

### Switch from Snap Firefox to Deb Firefox from Mozilla Team PPA and Enable Unattended Upgrade

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
sudo apt install firefox --allow-downgrades -y </dev/null
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
sudo rm /var/lib/snapd/desktop/applications/firefox*.desktop 2>/dev/null || true
sudo rm /var/lib/snapd/inhibit/firefox.lock 2>/dev/null || true
rm -r snap/firefox 2>/dev/null || true
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Switch from Snap Firefox to Deb Firefox ESR from Mozilla Team PPA

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
sudo apt install firefox --allow-downgrades -y </dev/null
sudo apt autoremove firefox --purge -y </dev/null 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y </dev/null
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
sudo rm /var/lib/snapd/desktop/applications/firefox*.desktop 2>/dev/null || true
sudo rm /var/lib/snapd/inhibit/firefox.lock 2>/dev/null || true
rm -r snap/firefox 2>/dev/null || true
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Switch from Snap Thunderbird to Deb Thunderbird from Mozilla Team PPA

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
sudo apt install thunderbird --allow-downgrades -y </dev/null
sudo rm /var/lib/snapd/desktop/applications/thunderbird*.desktop 2>/dev/null || true
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Enable Unattended Upgrade for Packages from Mozilla Team PPA

```
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Switch from Deb Firefox from Mozilla Team PPA Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/firefox
sudo apt autoremove firefox --purge -y </dev/null
sudo snap install firefox
```

### Switch from Deb Firefox ESR from Mozilla Team PPA Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/firefox
sudo apt autoremove firefox-esr --purge -y </dev/null
sudo snap install firefox
```

### Switch Deb Thunderbird from Mozilla Team PPA Back to Snap Thunderbird

```
sudo rm -rf /etc/apt/preferences.d/thunderbird
sudo apt autoremove thunderbird --purge -y </dev/null
sudo snap install thunderbird
```

### Disable Unattended Upgrade for Packages from Mozilla Team PPA

```
sudo rm /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Remove Mozilla Team PPA

```
sudo add-apt-repository -r ppa:mozillateam/ppa
```

### Switch Deb Chromium from XtraDeb PPA Back to Snap Chromium

```
sudo rm -rf /etc/apt/preferences.d/chromium
sudo apt autoremove chromium* --purge -y </dev/null
sudo snap install chromium
```

### Remove XtraDeb PPA

```
sudo add-apt-repository -r ppa:xtradeb/apps
```

### References

- Archisman Panigrahi, igi, Organic Marble, eddygeek, Yogev Neumann, & OMG Ubuntu. (2024). How to install Firefox as a traditional deb package (without snap) in Ubuntu 22.04 or later versions? <https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22>
- Archisman Panigrahi & BeastOfCaerbannog. (2024). How to install Thunderbird as a traditional deb package without snap in Ubuntu 24.04 and later versions? <https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2>
- Mitsuya Shibata. (2023). \[ubuntu-jp:6617\] Re: PPA にある Firefox の deb パッケージでのみ fcitx5 で日本語入力ができない問題. <https://lists.ubuntu.com/archives/ubuntu-jp/2023-May/006616.html>
- Schwarzer Kater. (2025). Kubuntu get rid of Snap. <https://gitlab.com/scripts94/kubuntu-get-rid-of-snap>
- Darin et al. (2025). Firefox and Chromium snaps preventing apps and devices from working correctly. <https://bugs.launchpad.net/snapd/+bug/1972762>

### My Related Repositories

* [**ubuntu-setup-with-vnc-and-gpu**](https://github.com/Willie169/ubuntu-setup-with-vnc-and-gpu)
* [**dual-boot-windows-linux-and-recovery**](https://github.com/Willie169/dual-boot-windows-linux-and-recovery)
