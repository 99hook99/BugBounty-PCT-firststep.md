IFACE=$(ip route | awk '/default/ {print $5; exit}')

systemctl restart systemd-resolved
resolvectl dns "$IFACE" 1.1.1.1 8.8.8.8
resolvectl domain "$IFACE" '~.'
resolvectl flush-caches

cp /etc/nsswitch.conf /etc/nsswitch.conf.bak
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
sed -i 's/^hosts:.*/hosts: files mymachines resolve [!UNAVAIL=return] dns myhostname/' /etc/nsswitch.conf

systemctl restart systemd-resolved

getent ahosts google.com
ping -c 3 google.com
apt update
