# 1章 PCの仕組みとハローワールド

* OSがない状態でコンピュータの画面にメッセージを表示してみるよ
* OSを使わないということは、コンピュータの生の機能をつかうということだよ
* コンピュータはデジタル回路でつくられていて 2 進数しか使えないのに、どうして文字が表示できるんだろう？
    * バイナリエディタを使ったプログラミングを通して、仕組みをかんたんに説明するよ。
        * その後で、C言語を使ったメッセージの表示をやり直してみるよ

---

```
$ brew install qemu
...
$ qemu-img create -f raw disk.img 200M
Formatting 'disk.img', fmt=raw size=209715200
$ md5 disk.img                        
MD5 (disk.img) = 3566de3a97906edb98d004d6b947ae9b
$ brew install dosfstools
...
$ mkfs.fat -n 'MIKAN OS' -s 2 -f 2  -R 32 -F 32 disk.img
mkfs.fat 4.2 (2021-01-31)
$ md5 disk.img                                          
MD5 (disk.img) = 0867d991f6d3b13f6e84484e39a81cab
```

もっかい同じ事やったら disk.img の md5 が変わったので日付とかのメタインフォがはいっているんだろうな。

`qemu-img` で作成した `disk.img` をFATでフォーマットし、アタッチ。作成したバイナリを `/EFI/BOOT/BOOTX64.EFI/bootx64.efi` として配置後、デタッチ。

```
$ mkdir mnt -p
$ hdiutil attach -mountpoint mnt disk.img 
/dev/disk4  /Users/sanosei/github.com/say3no/try_mikanos/01/mnt
$ mkdir -p mnt/EFI/BOOT
$ cp bootx64.efi mnt/EFI/BOOT/BOOTX64.EFI
cp: bootx64.efi: could not copy extended attributes to mnt/EFI/BOOT/BOOTX64.EFI: Attribute not found
$ ls -hal mnt/EFI/BOOT/BOOTX64.EFI 
-rwxrwxrwx  1 sanosei  staff   1.5K  8 22 01:25 mnt/EFI/BOOT/BOOTX64.EFI*
$ hdiutil detach mnt
"disk4" ejected.
```

fdってなんだ？ フロッピーディスクか？ OVMFってなんだ？

```
$ curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_CODE.fd
$ md5 OVMF_CODE.fd 
MD5 (OVMF_CODE.fd) = 7e393c8d1e71ae63ba9ddcac2e04fe49

$ curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_VARS.fd
$ md5 OVMF_VARS.fd 
MD5 (OVMF_VARS.fd) = bb627eb9250889d6a5f80e27cb6f3cd5
```

https://wiki.archlinux.jp/index.php/OVMF_%E3%81%AB%E3%82%88%E3%82%8B_PCI_%E3%83%91%E3%82%B9%E3%82%B9%E3%83%AB%E3%83%BC