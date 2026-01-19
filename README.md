## switch-firefox-from-snap-to-deb

Scripts to switch Firefox and Thunderbird from Snap to `.deb` version from Mozilla Team PPA and fix possible Fcitx5 not working in Firefox from PPA.

### Table of Contents

* [Script to Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended)](#script-to-switch-from-snap-firefox-and-thunderbird-to-deb-firefox-esr-and-thunderbird-enable-unattended-upgrade-and-fix-possible-fcitx5-not-working-in-firefox-from-ppa-recommended)
* [Script to Switch from Snap Firefox to Deb Firefox ESR](#script-to-switch-from-snap-firefox-to-deb-firefox-esr)
* [Script to Switch from Snap Firefox to Deb Firefox](#script-to-switch-from-snap-firefox-to-deb-firefox)
* [Script to Switch from Snap Thunderbird to Deb Thunderbird](#script-to-switch-from-snap-thunderbird-to-deb-thunderbird)
* [Script to Enable Unattended Upgrade for Packages from Mozilla Team PPA](#script-to-enable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Script to Switch from Deb Firefox ESR Back to Snap Firefox](#script-to-switch-from-deb-firefox-esr-back-to-snap-firefox)
* [Script to Switch from Deb Firefox Back to Snap Firefox](#script-to-switch-from-deb-firefox-back-to-snap-firefox)
* [Script to Switch Deb Thunderbird Back to Snap Thunderbird](#script-to-switch-deb-thunderbird-back-to-snap-thunderbird)
* [Script to Disable Unattended Upgrade for Packages from Mozilla Team PPA](#script-to-disable-unattended-upgrade-for-packages-from-mozilla-team-ppa)
* [Remove Mozilla Team PPA](#remove-mozilla-team-ppa)
* [Script to Fix Possible Fcitx5 not Working in Firefox from PPA](#script-to-fix-possible-fcitx5-not-working-in-firefox-from-ppa)
* [References](#references)
* [My Related Repositories](#my-related-repositories)

### Script to Switch from Snap Firefox and Thunderbird to Deb Firefox ESR and Thunderbird, Enable Unattended Upgrade, and Fix Possible Fcitx5 not Working in Firefox from PPA (Recommended)

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1

Package: thunderbird*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/mozilla
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove --purge firefox 2>/dev/null || true
sudo apt remove firefox --purge -y 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y
sudo rm -f /etc/apparmor.d/usr.bin.thunderbird
sudo rm -f /etc/apparmor.d/local/usr.bin.thunderbird
sudo systemctl stop var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo snap remove --purge thunderbird || true
sudo apt install thunderbird --allow-downgrades -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
sudo ln -sf /etc/apparmor.d/firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/firefox
```

### Script to Switch from Snap Firefox to Deb Firefox ESR

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/mozilla-firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove --purge firefox 2>/dev/null || true
sudo apt remove firefox --purge -y 2>/dev/null || true
sudo apt install firefox-esr --allow-downgrades -y
```

### Script to Switch from Snap Firefox to Deb Firefox

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: firefox*
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/mozilla-firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove --purge firefox 2>/dev/null || true
sudo apt remove firefox --purge -y 2>/dev/null || true
sudo apt install firefox --allow-downgrades -y
```

### Script to Switch from Snap Thunderbird to Deb Thunderbird

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
sudo snap remove --purge thunderbird 2>/dev/null || true
sudo apt install thunderbird --allow-downgrades -y
```

### Script to Enable Unattended Upgrade for Packages from Mozilla Team PPA

```
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-mozilla
```

### Script to Switch from Deb Firefox ESR Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/mozilla-firefox
sudo apt remove firefox-esr --purge -y
sudo snap install firefox
```

### Script to Switch from Deb Firefox Back to Snap Firefox

```
sudo rm -rf /etc/apt/preferences.d/mozilla-firefox
sudo apt remove firefox --purge -y
sudo snap install firefox
```

### Script to Switch Deb Thunderbird Back to Snap Thunderbird

```
sudo rm -rf /etc/apt/preferences.d/thunderbird
sudo apt remove thunderbird --purge -y
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

- Archisman Panigrahi, igi, Organic Marble, eddygeek, Yogev Neumann, & OMG Ubuntu (2024). How to install Firefox as a traditional deb package (without snap) in Ubuntu 22.04 or later versions? <https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22>.
- Archisman Panigrahi & BeastOfCaerbannog (2024). How to install Thunderbird as a traditional deb package without snap in Ubuntu 24.04 and later versions? <https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2>.
- Mitsuya Shibata (2023). \[ubuntu-jp:6617\] Re: PPA にある Firefox の deb パッケージでのみ fcitx5 で日本語入力ができない問題. <https://lists.ubuntu.com/archives/ubuntu-jp/2023-May/006616.html>

### My Related Repositories

* [**ubuntu-setup-with-vnc-and-gpu**](https://github.com/Willie169/ubuntu-setup-with-vnc-and-gpu)
* [**https://github.com/Willie169/dual-boot-windows-linux-and-recovery**](https://github.com/Willie169/dual-boot-windows-ubuntu)
* [**dual-boot-windows-linux-and-recovery**](https://github.com/Willie169/dual-boot-windows-linux-and-recovery)
