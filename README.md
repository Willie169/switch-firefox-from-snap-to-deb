## switch-firefox-from-snap-to-deb
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
sudo apt install firefox -y
echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:$(lsb_release -cs)";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-firefox
```

### Notes

Fcitx5 input method doesn't work with the `.deb` version of Firefox provided by `ppa:mozillateam/ppa`.

### Sources

- Archisman Panigrahi, igi, Organic Marble, eddygeek, Yogev Neumann, & OMG Ubuntu (2024). How to install Firefox as a traditional deb package (without snap) in Ubuntu 22.04 or later versions? [https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22](https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22).
- Archisman Panigrahi & BeastOfCaerbannog (2024). How to install Thunderbird as a traditional deb package without snap in Ubuntu 24.04 and later versions? [https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2](https://askubuntu.com/questions/1513445/how-to-install-thunderbird-as-a-traditional-deb-package-without-snap-in-ubuntu-2).
