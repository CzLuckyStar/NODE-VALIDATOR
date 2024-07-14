Penulis: [Naufal](https://twitter.com/0xfal)

# Pengenalan
> [!NOTE]
> Aligned Layer adalah ZK _verification layer_ yang dibangun di atas EigenLayer, memungkinkan verifikasi yang hemat biaya untuk SNARK _proof_, memanfaatkan keamanan _validator_ Ethereum tanpa limitasi Ethereum.

### Investor
![image](https://github.com/user-attachments/assets/5eb9cd67-62e9-41ed-a6b5-34ca27c9c57a)


# Tutorial _Validator Node_
Bab ini berisi tutorial cara menjalankan _validator node_.

## _Requirements_
Yang diperlukan untuk menjalankan _validator node_:

Komputer dengan spesifikasi:

| ✅ Linux | ✅ macOS | ✅ Windows (Native / WSL) |
| ------------- | ------------- | ------------- |

| Part | Minimum | Recommended |
| ------------- | ------------- | ------------- |
| CPU | - | 4 Core |
| RAM | - | 16 GB |
| SSD | - | 160 GB |

Tutorial ini dibuat menggunakan sistem operasi Linux (Ubuntu), untuk sistem operasi lainnya mungkin akan sedikit berbeda ([cek referensi](https://github.com/ZuperHunt/Aligned-Layer-Validator#acknowledgments)).

## _Dependencies_
Yang perlu dilakukan sebeleum menjalankan _validator node_:

### Instalasi `Go`
```
wget https://go.dev/dl/go1.22.1.linux-amd64.tar.gz
```
Konfigurasi _environment_ / _profile_:
```
rm -rf /$HOME/go && tar -C /usr/local -xzf go1.22.1.linux-amd64.tar.gz
export PATH=$PATH:/$HOME/go/bin
```

### Instalasi `Rust`
```
curl https://sh.rustup.rs -sSf | sh
```
Konfigurasi _environment_ / _profile_:
```
source "$HOME/.cargo/env"
```

### Instalasi `Ignite CLI`
```
curl https://get.ignite.com/cli! | bash
sudo curl https://get.ignite.com/cli | sudo bash
sudo mv ignite /usr/local/bin/
```

### Instalasi `Make`
```
sudo apt install make
```

## Menjalankan _Validator Node_

### Buat `tmux`
```
tmux
```

### _Clone Repository_
```
git clone https://github.com/yetanotherco/aligned_layer_tendermint.git
cd aligned_layer_tendermint
```

### Pengaturan _Node_
Ubah `<your-node-name>` menjadi terserahmu.
```
make clean
export PEER_ADDR=91.107.239.79,116.203.81.174,88.99.174.203,128.140.3.188
bash setup_node.sh <your-node-name>
```

### Jalankan _Node_
```
alignedlayerd start
```

> [!CAUTION]
> **WARNING!!!** JANGAN _TERMINATE_ TERMINAL YANG LAGI _RUNNING NODE_ UNTUK MELANJUTKAN KE STEP SELANJUTNYA, BUAT/SPLIT TMUX BARU atau TERMINAL BARU.

### Buat Akun Aligned Layer
Ubah `<account-name>` menjadi terserahmu.

Simpan _output_ yang dihasilkan dari menjalankan command ini, _copy address_ `alignedxxxx` mu untuk klaim _faucet_.
```
alignedlayerd keys add <account-name>
```

### Klaim _Faucet_
_Paste address_ `alignedxxxx` mu di [AlignedLayer Faucet](https://faucet.alignedlayer.com), klik _Request Tokens_.
![image](https://github.com/user-attachments/assets/d28c5e5b-c9f4-4d53-8bdf-cfc9fa6c157d)


### Cek _Balance_
Memastikan kalau _faucet_-nya berhasil masuk ke akunmu, wajib punya _balance_ untuk di-_stake_ jadi _validator_.

Ubah `<account-address-or-name>` menjadi sesuai akunmu yang sudah dibuat sebelumnya.
```
alignedlayerd query bank balances <account-address-or-name>
```

### Registrasi _Validator_
Ubah `<account-address-or-name>` menjadi sesuai akunmu yang sudah dibuat sebelumnya.
```
bash setup_validator.sh <account-name-or-address> 1050000stake
```

### Cek _Validator_
Cek apakah _validator_ mu sudah muncul di [explorer Alignedlayer](https://explorer.alignedlayer.com/alignedlayer/uptime), dan pastikan normal.
![image](https://github.com/user-attachments/assets/302f19fd-8a29-4d78-9756-b7c877054218)



## Change Logs

* 0.0.1
  * _Initial release_

## Acknowledgments

Referensi
* [_Joining Our Testnet_](https://github.com/yetanotherco/aligned_layer_tendermint#joining-our-testnet)
* [tmux _cheatsheet_](https://quickref.me/tmux.html)
