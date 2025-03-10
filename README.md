
# 📘 DN42 WireGuard & BIRD Multi-Peer Configuration (Dalonet)

### 🏷️ Author: Dalonet / PT Dalahas Local Network  
### 📅 Last Updated: 2025-03-08  
### 📡 BGP ASN: `4242420011`

---

## 📁 Struktur Konfigurasi WireGuard (3 Peer Setup)

### 📂 `wg-maraun.conf`

```ini
[Interface]
PrivateKey = <private_key_maraun>
PostUp = /sbin/ip addr add dev wg-maraun 172.20.10.33/27 peer 172.20.12.197/32
PostUp = /sbin/ip addr add dev wg-maraun fe80::1/64 peer fdb3:4cc3:3bfb::1
PostDown = /sbin/ip addr del 172.20.10.33/27 dev wg-maraun
PostDown = /sbin/ip addr del fe80::1/64 dev wg-maraun
Table = off
ListenPort = 51820

[Peer]
PublicKey = rWTIK93+XJaP4sRvrk1gqXxAZgkz6y/axLC4mjuay1I=
Endpoint = 139.180.159.8:20011
AllowedIPs = 0.0.0.0/0, ::/0
```

---

### 📂 `wg-surgebyte.conf`

```ini
[Interface]
PrivateKey = <private_key_surgebyte>
PostUp = /sbin/ip addr add dev wg-surgebyte 172.20.10.34/27 peer 172.20.12.197/32
PostUp = /sbin/ip addr add dev wg-surgebyte fe80::2/64 peer fd23:4e25:2ec8:18::1
PostDown = /sbin/ip addr del 172.20.10.34/27 dev wg-surgebyte
PostDown = /sbin/ip addr del fe80::2/64 dev wg-surgebyte
Table = off
ListenPort = 51821
MTU = 1360

[Peer]
PublicKey = cSgqLTP0SEcCg54DQBbCh3vfhN15YkVK7sjGGpFXDH0=
Endpoint = 172.21.99.18:30011
AllowedIPs = 0.0.0.0/0, ::/0
```

---

### 📂 `wg-routedbits.conf`

```ini
[Interface]
PrivateKey = <private_key_routedbits>
PostUp = /sbin/ip addr add dev wg-routedbits 172.20.10.35/27 peer 172.20.19.66/32
PostUp = /sbin/ip addr add dev wg-routedbits fe80::3/64 peer fdb1:e72a:343d::3
PostDown = /sbin/ip addr del 172.20.10.35/27 dev wg-routedbits
PostDown = /sbin/ip addr del fe80::3/64 dev wg-routedbits
Table = off
ListenPort = 51822
MTU = 1340

[Peer]
PublicKey = NyDLBU8swSAVZPficE7YhvJw0/KqJi43bwVfaxdgKW0=
Endpoint = router.cgk1.routedbits.com:50011
AllowedIPs = 0.0.0.0/0, ::/0
PersistentKeepalive = 25
```

---

## 🚀 Autostart WireGuard on Boot (Opsional)

```bash
sudo systemctl enable wg-quick@wg-maraun
sudo systemctl enable wg-quick@wg-surgebyte
sudo systemctl enable wg-quick@wg-routedbits
```

---

## 🐦 Contoh Konfigurasi BIRD

Pastikan tidak lupa gunakan `%wg-xxx` pada neighbor IPv6 link-local!

```bird
protocol bgp dn42_maraun {
  local as 4242420011;
  neighbor fe80::1%wg-maraun as 4242422225;
  ipv4 {
    import all;
    export all;
  };
  ipv6 {
    import all;
    export all;
  };
}

protocol bgp dn42_surgebyte {
  local as 4242420011;
  neighbor fe80::2%wg-surgebyte as 4242422234;
  ipv4 {
    import all;
    export all;
  };
  ipv6 {
    import all;
    export all;
  };
}

protocol bgp dn42_routedbits {
  local as 4242420011;
  neighbor fe80::3%wg-routedbits as 4242420207;
  ipv4 {
    import all;
    export all;
  };
  ipv6 {
    import all;
    export all;
  };
}
```

---

## 📌 Tips Penting

- Jangan lupa `%interface` untuk neighbor link-local IPv6.
- `Table = off` mencegah WireGuard mengubah routing default sistem.
- Pastikan firewall (iptables/nftables) tidak memblokir port WireGuard.
- Gunakan `tcpdump -i wg-*` saat debugging koneksi.

---

## 📫 Kontak

**Dalonet**  
📧 admin@dalonet.com  
🌐 https://dalonet.com  
🛰️ DN42: `dalonet.dn42`
