# Preparando o laptop

## Definições da máquina

Para fins de `storytelling`:

```
Comprei um Lenovo ThinkPad T480 de segunda-mão, em ótimas condições, num preço incrível. O antigo dono usava para trabalho 👀 e acabou comprando o equipamento da empresa 👀. Como não estava mais usando, resolveu vendê-lo.

Hardware: Intel Core i7 8ª geração, 32GB de ram e 500GB de SSD. 

Eu, como boa paranoica em segurança que sou, resolvi um wipe completo do disco antes de instalar o OS. Nunca se sabe.
```

## Objetivos

- Apagar 100% do conteúdo anterior;
- Remover:
  - Windows;
  - Partições OEM / recovery;
  - EFI antigo;
  - Metadados corporativos;
  - Criar base limpa;
  
Passos:

[1. Criar pendrive do Ubuntu 25.10;](#1-criar-pendrive-do-ubuntu-2510)

[2. Ajustar BIOS/UEFI;](#2-ajustar-biosuefi)

[3. Bootar em Ubuntu Live;](#3-bootar-em-live-ubuntu)

[4. Zerar o disco manualmente;](#4-zerar-o-disco-manualmente)

[5. Recriar tabela de partição;](#5-recriar-tabela-de-particao)

[6. Instalar Ubuntu 25.10;](#6-instalar-ubuntu-2510)

## 1. Criar pendrive do Ubuntu 25.10
Aqui, usei [o guia oficial](https://documentation.ubuntu.com/desktop/en/latest/how-to/create-a-bootable-usb-stick/) do Ubuntu para fazer um pendrive bootável. No meu caso, usei o [balenaEtcher](https://etcher.balena.io/).

## 2. Ajustar BIOS/UEFI
Antes da instalação, acessei a BIOS e ajustei algumas configurações: desabilitei o Secure Boot, deixei habilitado o TPM 2.0. No Boot Mode escolhi UEFI.

## 3. Bootar em Ubuntu Live
Bootei pelo pendrive e selecionei a opção Ubuntu Live para iniciar o ambiente.

## 4. Zerar o disco manualmente
No terminal do Ubuntu Live, usei o comando `lsblk` para identificar o disco. O SSD foi identificado como **nvme0n1**, e continha outras partições (recovery, por exemplo).

Em seguida, com certeza do wipe completo do disco **nvme0n1**, usei o comando `sudo wipefs -a /dev/nvme0n1`. [Este comando remove o filesystem, RAID e assinaturas de tabelas de partição.](https://manpages.ubuntu.com/manpages/xenial//man8/wipefs.8.html).

Em seguida, usei o comando `sudo sgdisk --zap-all /dev/nvme0n1`. [O qual](https://www.rodsbooks.com/gdisk/sgdisk.html) apaga tanto a tabela de partição GUID (GPT) quanto os dados do Master Boot Record (MBR) (ex.: /dev/nvme0n1p1). O resultado é um disco completamente resetado pra receber novas particições. 

<span style="color:red"> ‼️ ATENÇÃO ‼️ O comando a seguir é <b>destrutivo</b> e sobrescreve o header do disco:</span><br>
Por fim, limpei os headers do disco com o comando `sudo dd if=/dev/zero of=/dev/nvme0n1 bs=1M count=1024 status=progress`.

## 5. Recriar tabela de partição

A tabela de partição foi recriada manualmente usando:

```
> sudo parted /dev/nvme0n1
> mklabel gpt
> quit
```

## 6. Instalar Ubuntu 25.10 

Iniciei a instalação do OS, clicando em *Install Ubuntu*. Na etapa de particionamento, selecionei:

-> Erase disk and install Ubuntu

A partir deste ponto o sistema passou a ser instalado em uma base completamente limpa.

---

>A partir daqui, o foco passa a ser a configuração do ambiente de virtualização e redes do homelab.<br>
>[Parte 02 — Configuração do ambiente de virtualização (QEMU/KVM + libvirt)](./part-02-pt.md)
