# 「BGP Unnumbered で遊んでみた」 デモ環境

## 概要

「[BGP Unnumbered で遊んでみた](https://www.slideshare.net/akira6592/bgp-233482295/akira6592/bgp-233482295)」のでもで利用した環境の Vagrantfile です。

起動直後は、4台間ですべて BGP ネイバーを張っている状態になります。

## 構成図

![構成図](./images/topology.png)

## 起動方法
```sh
vagrant up
```

## 想定環境 
- Virtual Box 6.0.14
- Vagrant 2.2.8