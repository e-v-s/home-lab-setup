## Overview

Este repositório documenta **todo o processo de concepção, implementação e
evolução** de um HomeLab voltado para segurança ofensiva e defensiva, com foco em:

- Segmentação de rede realista;
- Isolamento de ambientes vulneráveis;
- Análise de malware em ambiente controlado;
- Observabilidade de tráfego e eventos;
- Boas práticas de arquitetura (enterprise-like).

O laboratório foi projetado para ser **modular**, **seguro** e **reproduzível**.

## Objetivos

- Separação clara entre atacante, vítimas e analista;
- Uso de firewall como ponto central de controle;
- Um cenário ativo por vez (redução de ruído e consumo);
- Zero trust no host físico;
- Possibilidade de rollback completo via snapshots.

## Architecture

<p align="center">
  <img src="./diagrams/diagrama-homelab-v02.png" width="800">
</p>

### Zonas Lógicas

- **LAB-WIN**: Ambiente Windows (AD / endpoints);
- **LAB-DVWA**: Web application vulnerável;
- **LAB-METASPLOIT**: Serviços legados vulneráveis;
- **LAB-CHRONOS**: Ambiente IoT / legacy;
- **LAB-MALWARE**: Análise e contenção de malware.

## Network & Hypervisor

- **Hypervisor**: Proxmox VE (KVM);
- **Virtual Networking**: Linux Bridges;
- **Firewall / Router**: pfSense (VM dedicada);
- **Routing Strategy**: Inter-zone routing via pfSense.

## Labs & Cenários

| Scenario | Description |
|--------|------------|
| LAB-WIN | Ataques a ambiente Windows (logs, lateral movement) |
| LAB-DVWA | Testes de vulnerabilidades web |
| LAB-METASPLOIT | Exploração de serviços inseguros |
| LAB-CHRONOS | Análise de dispositivos legacy / IoT |
| LAB-MALWARE | Static e dynamic malware analysis |

## Ferramentas Usadas

### Virtualização & OS
<a href="https://www.qemu.org/" target="_blank">
  <img src="https://img.shields.io/badge/QEMU-000000?logo=qemu&logoColor=white" />
</a>
<a href="https://www.linux-kvm.org/" target="_blank">
  <img src="https://img.shields.io/badge/KVM-00599C?logo=linux&logoColor=white" />
</a>
<a href="https://libvirt.org/" target="_blank">
  <img src="https://img.shields.io/badge/libvirt-5A2D82?logo=libvirt&logoColor=white" />
</a>
<a href="https://ubuntu.com/" target="_blank">
  <img src="https://img.shields.io/badge/Ubuntu-E95420?logo=ubuntu&logoColor=white" />
</a>

### Network & Segurança
<a href="https://www.pfsense.org/" target="_blank">
  <img src="https://img.shields.io/badge/pfSense-212121" />
</a>
<a href="https://en.wikipedia.org/wiki/Computer_network" target="_blank">
  <img src="https://img.shields.io/badge/Networking-blue" />
</a>
<a href="https://en.wikipedia.org/wiki/Firewall_(computing)" target="_blank">
  <img src="https://img.shields.io/badge/Firewall-red" />
</a>

### Segurança Defensiva & Ofensiva
<a href="https://www.kali.org/" target="_blank">
  <img src="https://img.shields.io/badge/Kali_Linux-557C94?logo=kali-linux&logoColor=white" />
</a>
<a href="https://github.com/mandiant/flare-vm" target="_blank">
  <img src="https://img.shields.io/badge/FlareVM-purple" />
</a>
<a href="https://docs.remnux.org/" target="_blank">
  <img src="https://img.shields.io/badge/REMnux-blue" />
</a>

## Referências

### Documentação oficial
- QEMU — https://www.qemu.org/documentation/
- KVM — https://www.linux-kvm.org/page/Documents
- libvirt — https://libvirt.org/docs.html
- pfSense Docs — https://docs.netgate.com/pfsense/en/latest/
- Ubuntu Server — https://ubuntu.com/server/docs
- Kali Linux — https://www.kali.org/docs/
- FLARE VM — https://github.com/mandiant/flare-vm
- REMnux — https://docs.remnux.org/

---

>Na próxima etapa, começo configurando o ambiente inicial.<br>
>[Parte 01 - Preparando o laptop](./part-01-pt.md)

---

> Este lab é um projeto de aprendizado contínuo.
> As decisões de design são documentadas, revisadas e melhoradas ao longo do tempo.
> English version comming soon.

--- 

> This lab is a continuous learning project.  
> Design decisions are documented, revisited and improved over time.
