# lidar_emulator_raspberry

LiDARのpcapファイルをラズパイ上で再生し、偽装LiDARとして振る舞います。

# 必要なもの

* 再生用のパケットファイル
* Raspberry Pi
  * 動作環境: Raspberry Pi 3B, Ubuntu Server 22.04 64bit
* LANケーブル

# 使い方

## 1. LiDARのパケットファイルを用意する

- Velodyne・Ouster・Hesaiなどブロードキャストする系のやつなら動くはず

## 2. LiDARのIPアドレスを決める

- ここでは`192.168.1.201`とする

## 2. このリポジトリをラズパイ上でClone

- `git clone https://github.com/organic-nailer/lidar_emulator_raspberry.git`

## 3. コードを書き換える

以下の行に2. で決めたアドレスを入れる

https://github.com/organic-nailer/lidar_emulator_raspberry/blob/008f333e3a522092400e596dabfc5a4adaec909e/src/lib.rs#L6

## 3. ビルド

Rustが入っていない場合は入れる

- `cd lidar_emulator_raspberry`
- `cargo build --release`

## 4. pcapファイルをラズパイに入れる

- `lidar_emulator_raspberry/xxx.pcap`に置く

## 5. Raspberry PiのIPアドレスを固定する

- `/etc/netplan/99-network.yaml`を新規作成
- 以下のように書き込む

```
network:
  ethernets:
    eth0:
      addresses:
        - 192.168.1.201/24
      nameservers:
        addresses:
          - 8.8.8.8
      routes:
        - to: default
          via: 192.168.1.1
  version: 2
```

> `192.168.1.201`の部分は先程指定したアドレス
> `eth0`は`ip a`とかで確認してください

- `sudo netplan apply`

## 6. 動かす

in `lidar_emulator_raspberry`

- `./target/release/lidar_emulator_raspberry -r xxx.pcap`

## 7. LiDARと同じ用に繋いで観測する

例えばVeloViewとかで

# Options

- `-p`, `--port`: ポートを指定
- `-r`, `--repeat`: 無限ループさせる
