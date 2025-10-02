## switch-firefox-from-snap-to-deb

Scripts to switch Firefox and Thunderbird from Snap to `.deb` and fix Fcitx5 not working on Firefox on KDE Plasma.

### Script to Switch Firefox from Snap to Deb

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: *
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/mozilla-firefox
sudo rm -f /etc/apparmor.d/usr.bin.firefox
sudo rm -f /etc/apparmor.d/local/usr.bin.firefox
sudo systemctl stop var-snap-firefox-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-firefox-common-*.mount 2>/dev/null || true
sudo snap remove --purge firefox || true
sudo apt install firefox --allow-downgrades -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-firefox
```

### Script to Switch Firefox Back to Snap

```
sudo rm -rf /etc/apt/preferences.d/mozilla-firefox
sudo apt remove firefox
sudo snap install firefox
```

### Script to Switch Thunderbird from Snap to Deb

```
sudo add-apt-repository ppa:mozillateam/ppa -y
echo 'Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001

Package: *
Pin: release o=Ubuntu
Pin-Priority: -1' | sudo tee /etc/apt/preferences.d/thunderbird
sudo rm -f /etc/apparmor.d/usr.bin.thunderbird
sudo rm -f /etc/apparmor.d/local/usr.bin.thunderbird
sudo systemctl stop var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo systemctl disable var-snap-thunderbird-common-*.mount 2>/dev/null || true
sudo snap remove --purge firefox || true
sudo apt install thunderbird --allow-downgrades -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-thunderbird
```

### Script to Switch Thunderbird Back to Snap

```
sudo rm -rf /etc/apt/preferences.d/thunderbird
sudo apt remove thunderbird
sudo snap install thunderbird
```

### Remove Mozillateam PPA

```
sudo add-apt-repository -r ppa:mozillateam/ppa
```

### Fix for Fcitx5 not Working on Firefox on KDE Plasma

```
sudo ln -s /etc/apparmor.d/usr.bin.firefox /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/usr.bin.firefox
sudo apt update
sudo apt install kde-config-fcitx -y
```

### Sources

- Archisman Panigrahi, igi, Organic Marble, eddygeek, Yogev Neumann, & OMG Ubuntu (2024). How to install Firefox as a traditional deb package (without snap) in Ubuntu 22.04 or later versions? <https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22>.
- Archisman Panigrahi & BeastOfCaerbannog (2024). How to install Thunderbird as a traditional deb package without snap in Ubuntu 24.04 and later versions? <https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2>.
- Mitsuya Shibata (2023). \[ubuntu-jp:6617\] Re: PPA にある Firefox の deb パッケージでのみ fcitx5 で日本語入力ができない問題. <https://lists.ubuntu.com/archives/ubuntu-jp/2023-May/006616.html>
