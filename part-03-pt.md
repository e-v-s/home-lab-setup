# Arquitetura de Rede (Design Lógico)

Este laboratório foi projetaado para dar suporte à minha prática na área de redes, segurança ofensiva e defensiva, análise de malware e troubleshooting, usando QEMU/KVM com libvirt e um firewall virtual pfSense.

A arquitetura segue princípios estritos de isolamento para evitar contaminação cruzada entre os laboratório, mas que ainda assim permite cenários de ataque e de análises realistas.

## Princípios de Design

- A máquina física, com Ubuntu, não participa dos ataques;
- pfSense é implementado como uma VM e age como o único gateway para todas as redes do lab;
- Cada lab está isolado na camada 2 usando redes dedicadas pelo libvirt;
- A VM Kali pode se conectar à várias redes mas apenas **uma rede de cada vez**;
- Os ambientes de análise de malware são completamente isolados das ferramentas e VMs de cunho ofensivo;
- Nenhum lab depende da rede física do host.

## Arquitetura de Alto-nível

- **Host OS:** Ubuntu (ThinkPad T480);
- **Hypervisor:** QEMU/KVM + libvirt (CLI via `virsh`);
- **Firewall/Gateway:** pfSense (VM com diversas NICs);
- **VM Atacante:** Kali Linux;
- **Alvos:** Windows Server 2019, Windows 10, Windows 7, Metasploitable 2, Chronos 1, DVWA;
- **Defesa / Análise:** REMnux, FLARE VM e o próprio pfSense.

Todo o roteamento e filtragem entre as redes é controlado pelo pfSense.

## Segmentação de Rede

Cada lab é implementado com uma rede islada via libvirt com sua própria sub-rede e interface de gateway no pfSense.

| Rede             | Propósito                                  | Sub-rede         |
|------------------|--------------------------------------------|------------------|
| WAN-NAT          | Acesso à internet via pfSense              | 192.168.100.0/24 |
| LAB-WIN          | Ambiente Windows Corporativo (AD + users)  | 10.10.10.0/24    |
| LAB-METASPTBL    | Metasploitable 2                           | 10.20.20.0/24    |
| LAB-CHRONOS      | Chronos 1                                  | 10.30.30.0/24    |
| LAB-DVWA         | Exploit de Aplicações Web                  | 10.40.40.0/24    |
| LAB-MALWARE      | Ambiente de Análise de Malware             | 10.50.50.0/24    |

## WAN-NAT - Rede WAN

A rede WAN é uma rede libvirt NAT usada exclusivamente pelo pfSense, permitindo:

- Acesso à internet para labs específicos;
- Nenhuma outra VM se conecta diretamenta à esta rede;
- Esta interface usa dhcp.

## LAB-WIN - Ambiente Windows Corporativo

Este lab simula um ambiente corporativo com ADDS e endpoins windows.

### Sub-rede
- Rede: `10.10.10.0/24`
- Gateway: `10.10.10.1` (pfSense)

### Hosts
- pfSense (LAN-WIN): `10.10.10.1`
- Windows Server (AD DS): `10.10.10.10`
- Windows 10 Cliente: `10.10.10.101`
- Windows 7 Cliente: `10.10.10.102`
- Kali Linux (quando ativo): `10.10.10.50`

O objetivo deste design é permitir testes realistas com ataques ADDS, movimento lateral e cenários do tipo empresa.

## LAB-METASPTBL - Metasploitable Lab

Ambiente dedicado para exploit de serviços vulneráveis em máquinas Unix.

### Sub-rede
- Network: `10.20.20.0/24`
- Gateway: `10.20.20.1`

### Hosts
- pfSense: `10.20.20.1`
- Metasploitable 2: `10.20.20.100`
- Kali Linux: `10.20.20.50`

## LAB-CHRONOS - Chronos 1

Este laboratório tem como objetivo praticar exploits de escalação de privilégio, RCE e network enum.

### Sub-rede
- Network: `10.30.30.0/24`
- Gateway: `10.30.30.1`

### Hosts
- pfSense: `10.30.30.1`
- Chronos 1: `10.30.30.100`
- Kali Linux: `10.30.30.50`

## LAB-DVWA 

Lab voltado para o exploit de aplicações web vulneráveis.

### Sub-rede
- Network: `10.40.40.0/24`
- Gateway: `10.40.40.1`

### Hosts
- pfSense: `10.40.40.1`
- DVWA VM: `10.40.40.100`
- Kali Linux: `10.40.40.50`

## LAB-MALWARE - Ambiente de Análise de Malware

Este lab é usado para engenharia reversa e análise de malware, e é completamente isolado das outras máquinas.

### Sub-rede
- Network: `10.50.50.0/24`
- Gateway: `10.50.50.1`

### Hosts
- pfSense: `10.50.50.1`
- REMnux: `10.50.50.10`
- FLARE VM: `10.50.50.11`

Nenhuma conexão com o Kali é permitida. O acesso à internet é controlado via pfSense. As amostras de malware são transferidas manualmente.

## Mapeamento das Interfaces via pfSense

Cada rede tem uma interface própria:

| Interface | Rede              |
|-----------|-------------------|
| em0       | WAN-NAT           |
| em1       | LAB-WIN           |
| em2       | LAB-METASPTBL     |
| em3       | LAB-CHRONOS       |
| em4       | LAB-DVWA          |
| em5       | LAB-MALWARE       |

Outras inferfaces podem ser adicionadas de acordo com o caso.

## Desneho de Rede - Kali Linux

A VM Kali é configurada com uma interface de rede para cada lab, de acordo com a tabela abaixo:

| Interface | Rede              |
|-----------|-------------------|
| eth0      | LAB-WIN           |
| eth1      | LAB-METASPTBL     |
| eth2      | LAB-CHRONOS       |
| eth3      | LAB-DVWA          |


<span style="color:red;"><b>‼️ IMPORTANTE ‼️</b></span><br>
Apenas UMA interface está ativa por vez, prevenindo interação acidental entre labs.

## Segurança

- Não existe roteamento entre redes;
- Para cada lab, as regras de firewall são definidas de forma explícita;
- A análise de malware está fisicamente e logicamente isolada;
- O tráfego de rede não sai dos lab para a rede do host físico.

## TODO

[ ] VLAN segmentation inside LAB-WIN
[ ] IDS/IPS deployment on pfSense
[ ] Traffic mirroring to REMnux
[ ] Centralized logging and alerting


