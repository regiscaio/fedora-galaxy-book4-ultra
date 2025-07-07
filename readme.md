# Fedora 42 Workstation no Samsung Galaxy Book4 Ultra

> [!IMPORTANT]
> **Atualizado em julho de 2025**
>
> Este guia reúne explicações técnicas, soluções práticas e informações úteis para usuários do Fedora 42 no Samsung Galaxy Book4 Ultra, com foco em áudio, câmera, leitor de digital e drivers NVIDIA.

| Especificação         | Detalhes                                                                            |
| --------------------- | :---------------------------------------------------------------------------------- |
| **Modelo**            | Samsung Galaxy Book4 Ultra (NP960XGL, NP960XGL-XG1BR, NP960XGLZ-EXP...)                         |
| **Tela**              | 16" WQXGA+ (2880 x 1800) AMOLED Touchscreen                                         |
| **Processador**       | Intel Core Ultra 9 185H (Meteor Lake, 16C/22T, até 5.1 GHz, 24 MB cache)            |
| **GPU**               | NVIDIA GeForce RTX 4070 Laptop 8 GB GDDR6                                           |
| **Memória RAM**       | 32 GB LPDDR5X (soldada, não expansível)                                             |
| **Armazenamento**     | 1 TB SSD NVMe PCIe Gen4 (expansível via slot M.2 secundário, dependendo do modelo)  |
| **Bateria**           | 76 Wh (integrada, íon-lítio)                                                        |
| **Carregador**        | 140 W USB-C (20V, 5A ou 28V, 5A)                                                    |
| **Portas**            | 2x Thunderbolt 4 (USB-C), 1x HDMI 2.1, 1x USB-A 3.2, 1x P2 (áudio), 1x MicroSD      |
| **Rede sem fio**      | Wi-Fi 6E (802.11ax), Bluetooth 5.3                                                  |
| **Webcam**            | 1080p FHD, sensor OmniVision OV02C10, IPU6 (MIPI)                                   |
| **Áudio**             | 2x Alto-falantes estéreo (AKG), microfone duplo, codec Realtek ALC298               |
| **Teclado**           | Teclado retroiluminado, layout ABNT2 (BR) ou US, sensor de digital integrado        |
| **Touchpad**          | Amplo, compatível com gestos Windows Precision                                      |
| **Leitor de Digital** | Integrado ao botão power, compatível com Windows Hello                              |
| **Dimensões**         | 355,4 x 250,4 x 16,5 mm                                                             |
| **Outros**            | Sensor de luz ambiente, suporte a Samsung Multi Control, Second Screen, Quick Share |
| **Certificações**     | ENERGY STAR, RoHS, FCC, CE                                                          |

* [Manual do Usuário](https://downloadcenter.samsung.com/content/UM/202506/20250618100447559/94xXGK_96xXGK_96xXGL_Win11_UM_ENG_Rev.2.0_250602.pdf)
* [Especificações](https://www.samsung.com/br/support/model/NP960XGL-XG1BR/)

> [!TIP]
> **Kernel recomendado:** Utilize o kernel **6.15 ou superior** para garantir o reconhecimento adequado do hardware do Galaxy Book4 Ultra. Versões anteriores apresentam limitações significativas de compatibilidade.
>
> Ferramentas como o [samsung-galaxybook-extras](https://github.com/joshuagrisham/samsung-galaxybook-extras) podem facilitar a configuração inicial e aplicar ajustes específicos para este modelo.
> 
> Caso você não seja um usuário avançado, recomendo que use o Windows 11, que já vem instalado de fábrica e funciona perfeitamente neste modelo.

---

## Índice

- [Fedora 42 Workstation no Samsung Galaxy Book4 Ultra](#fedora-42-workstation-no-samsung-galaxy-book4-ultra)
  - [Índice](#índice)
  - [Incompatibilidades](#incompatibilidades)
  - [Alto-falantes Internos (Realtek ALC298)](#alto-falantes-internos-realtek-alc298)
    - [Solução parcial: Script de Inicialização (HDA Verb)](#solução-parcial-script-de-inicialização-hda-verb)
    - [Verificação](#verificação)
  - [Câmera Interna (IPU6/OV02C10)](#câmera-interna-ipu6ov02c10)
    - [Solução parcial: usar webcam USB](#solução-parcial-usar-webcam-usb)
    - [Verificação](#verificação-1)
  - [Leitor de Digital (Fingerprint)](#leitor-de-digital-fingerprint)
    - [Solução parcial: reinstalar `fprintd` e `libfprint`, reconfigurar o sensor.](#solução-parcial-reinstalar-fprintd-e-libfprint-reconfigurar-o-sensor)
    - [Verificação](#verificação-2)
  - [Chip de Gráficos dedicados (NVIDIA RTX 4070)](#chip-de-gráficos-dedicados-nvidia-rtx-4070)
    - [Solução: Instalação do driver NVIDIA](#solução-instalação-do-driver-nvidia)
    - [Verificação](#verificação-3)
  - [Diagnósticos Rápidos](#diagnósticos-rápidos)
  - [Outros Relatos](#outros-relatos)

---

## Incompatibilidades


| Componente            |  Status Fedora 42  | Causa técnica resumida                                                             |
| --------------------- | :-------------------: | ---------------------------------------------------------------------------------- |
| **Áudio interno**     |     Não funciona      | Codec Realtek ALC298 não inicializa amplificador dos alto-falantes automaticamente |
| **Câmera interna**    |     Não funciona      | Driver IPU6 OV02C10 não aceita clock de 26 MHz, necessário para o sensor Samsung   |
| **Leitor de digital** |   Parcial/instável    | libfprint não suporta todos os sensores, problemas pós-suspensão                   |
| **NVIDIA RTX 4070**   | Funciona c/ ressalvas | Driver proprietário pode falhar após update de kernel, Secure Boot pode bloquear   |
| **BIOS/Firmware**     |    Riscos recentes    | Updates recentes causam throttling, falhas em docks e Bluetooth                    |

---

## Alto-falantes Internos (Realtek ALC298)

O Galaxy Book4 Ultra usa o codec **Realtek ALC298**. No Linux, o driver detecta o hardware, mas **não ativa automaticamente o amplificador dos alto-falantes internos**. Resultado: som apenas via HDMI, Bluetooth ou fone de ouvido. Nos meus testes com Fedora 42, usando o [script do GitHub](https://github.com/joshuagrisham/galaxy-book2-pro-linux/blob/main/sound/necessary-verbs.sh) e os passos abaixo, consegui fazer o com que ao menos os alto-falantes fossem reconhecidos, mas ainda sem funcionamento.

![alt text](img/settings-audio.png)

### Solução parcial: Script de Inicialização (HDA Verb)

1. Instale ferramentas:

```bash
sudo dnf install alsa-tools sof-firmware
```

2. Crie o script de inicialização com comandos HDA adequados:

```bash
sudo tee /usr/local/bin/galaxy-book4-audio-fix.sh
```

3. Dê permissão e crie o service:

```bash
sudo chmod +x /usr/local/bin/galaxy-book4-audio-fix.sh
```

Crie `/etc/systemd/system/speaker-init.service` com:

```bash
[Unit]
Description=Galaxy Book Speaker Fix
After=sound.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/galaxy-book4-audio-fix.sh

[Install]
WantedBy=multi-user.target
```

4. Ative:

```bash
sudo systemctl enable --now speaker-init.service
```

### Verificação
```bash
speaker-test -c 2 -t wav
```

Erro comum:

```
Não emite som e não retorna erro.
```

---

## Câmera Interna (IPU6/OV02C10)

O driver incluso no kernel 6.15 ou superior, reconhece o sensor OmniVision OV02C10, porém **não suporta o clock de 26 MHz** utilizado pela Samsung neste modelo, aceitando apenas sensores configurados para 19.2 MHz. Embora alguns usuários do **Galaxy Book3 Ultra** (NP960XFHZ-EXP) tenham conseguido utilizar a câmera interna, isso ocorreu porque o sensor deles, apesar de ser o mesmo modelo, opera em 19.2 MHz — diferente do **Galaxy Book4 Ultra** (NP960XGLZ-EXP), que permanece incompatível devido à diferença de clock.

![alt text](img/app-camera.png)

### Solução parcial: usar webcam USB

Ainda não há solução parcial ou definitiva para a câmera interna. A melhor alternativa é usar uma webcam USB externa.

### Verificação

```bash
v4l2-ctl --list-devices
```

Erro comum:

```
external clock 26000000 is not supported
probe with driver ov02c10 failed with error -22
```

O driver IPU6 não aceita o clock de 26 MHz necessário para a câmera interna.

---

## Leitor de Digital (Fingerprint)

O sensor é detectado via `fprintd`, mas pode falhar após suspensão ou não aceitar cadastro.

![alt text](img/settings-fingerprint.png)

### Solução parcial: reinstalar `fprintd` e `libfprint`, reconfigurar o sensor.

```bash
sudo dnf reinstall fprintd libfprint
sudo systemctl restart fprintd
fprintd-delete $USER
fprintd-enroll
sudo authselect enable-feature with-fingerprint
sudo authselect apply-changes
```

### Verificação

```bash
fprintd-verify
```

Erro comum:

```
Failed to discover prints
```

O sensor não consegue localizar a digital cadastrada anteriormente.

---

## Chip de Gráficos dedicados (NVIDIA RTX 4070)

Funciona bem com RPM Fusion, mas requer atenção com updates de kernel e Secure Boot com processo de MOK.

![alt text](img/settings-gpu.png)

### Solução: Instalação do driver NVIDIA

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf update
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
sudo reboot
```

Se falhar após update:

```bash
sudo akmods --force
sudo dracut --force
sudo reboot
```

Desative Secure Boot se for necessário.

### Verificação

```bash
nvidia-smi
```

Erro comum:

```
Unable to determine the device handle for GPU0: 0000:01:00.0: Unknown Error
No devices were found
```

O driver NVIDIA não está carregado corretamente ou o Secure Boot está bloqueando o módulo.

---

## Diagnósticos Rápidos

| Componente  | Comando de Diagnóstico                                                                                              |
| :---------- | :------------------------------------------------------------------------------------------------------------------ |
| **Kernel**  | `uname -r`                                                                                                          |
| **Áudio**   | `aplay -l && speaker-test -c 2 -t wav`                                                                            |
| **Câmera**  | `v4l2-ctl --list-devices && sudo dmesg \| grep -i ov02c10 && sudo dmesg \| grep -i sensor && lsmod \| grep ov02c10` |
| **Digital** | `fprintd-verify && journalctl -b \| grep -i fprint`                                                                 |
| **NVIDIA**  | `nvidia-smi && lsmod \| grep nvidia && sudo dmesg \| grep nvidia`                                                   |

## Outros Relatos

> [!NOTE]
> Estes links contêm relatos e experiências de outros usuários com o Galaxy Book4 Ultra no linux, abordando desde problemas de hardware até tentativas de soluções criativas.

- [Relato de tentativa de engenharia reversa para alto-falantes internos no EndeavourOS](https://github.com/dgunay/galaxy-book4-pro-reverse-engineering)
- [Relato com uso do Fedora 42 (KDE) com medições de desempenho da Intel Xe Iris](https://github.com/jusqua/galaxy-book4-linux)