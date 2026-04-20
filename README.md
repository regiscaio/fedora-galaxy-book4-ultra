# Fedora no Samsung Galaxy Book4 Ultra

## Instalação rápida

Para instalar a linha atual do suporte principal do notebook via repositório DNF:

```bash
sudo dnf config-manager addrepo --from-repofile=https://packages.caioregis.com/fedora/caioregis.repo
sudo dnf install galaxybook-camera galaxybook-setup akmod-galaxybook-ov02c10 akmod-galaxybook-max98390
```

Esse fluxo instala:

- o app de câmera com UI nativa do GNOME;
- o assistente gráfico de instalação e diagnóstico;
- o driver `ov02c10` empacotado como `akmod`.
- o suporte `MAX98390` empacotado como `akmod` para os alto-falantes internos.

> [!IMPORTANT]
> **Atualizado em 20 de abril de 2026**
>
> Este guia reúne explicações técnicas, soluções práticas e informações úteis para usuários do Fedora no Samsung Galaxy Book4 Ultra, com foco em áudio, câmera, leitor de digital e drivers NVIDIA.
>
> O conteúdo histórico abaixo foi **revalidado em 5 de abril de 2026** em um **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, rodando **Fedora 43** com kernel **6.18.8-200.fc43.x86_64**.
>
> A partir de **19 de abril de 2026**, a solução da câmera e o suporte dedicado aos alto-falantes internos passaram a ser mantidos em repositórios próprios, com driver, app de câmera com UI nativa do GNOME, assistente de instalação e driver de áudio separados. Este README continua como guia geral do notebook e aponta para esses projetos quando o assunto é webcam e áudio.
>
> A linha atual dos projetos dedicados foi **verificada em 20 de abril de 2026** em um **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, rodando **Fedora 44** com kernel **6.19.10-300.fc44.x86_64**, nas seguintes versões:
>
> - `fedora-galaxy-book-ov02c10`: **1.0.0-1**
> - `fedora-galaxy-book-camera`: **1.0.0**
> - `fedora-galaxy-book-setup`: **1.0.0-4**
> - `fedora-galaxy-book-max98390`: **1.0.0-1**

| Especificação       | Detalhes                                                                             |
| ----------------------- | :------------------------------------------------------------------------------------- |
| **Modelo**            | Samsung Galaxy Book4 Ultra (NP960XGL, NP960XGL-XG1BR, NP960XGLZ-EXP...)              |
| **Tela**              | 16" WQXGA+ (2880 x 1800) AMOLED Touchscreen                                          |
| **Processador**       | Intel Core Ultra 9 185H (Meteor Lake, 16C/22T, até 5.1 GHz, 24 MB cache)            |
| **GPU**               | NVIDIA GeForce RTX 4070 Laptop 8 GB GDDR6                                            |
| **Memória RAM**      | 32 GB LPDDR5X (soldada, não expansível)                                            |
| **Armazenamento**     | 1 TB SSD NVMe PCIe Gen4 (expansível via slot M.2 secundário, dependendo do modelo) |
| **Bateria**           | 76 Wh (integrada, íon-lítio)                                                       |
| **Carregador**        | 140 W USB-C (20V, 5A ou 28V, 5A)                                                     |
| **Portas**            | 2x Thunderbolt 4 (USB-C), 1x HDMI 2.1, 1x USB-A 3.2, 1x P2 (áudio), 1x MicroSD      |
| **Rede sem fio**      | Wi-Fi 6E (802.11ax), Bluetooth 5.3                                                   |
| **Webcam**            | 1080p FHD, sensor OmniVision OV02C10, IPU6 (MIPI)                                    |
| **Áudio**            | 2x Alto-falantes estéreo (AKG), microfone duplo, codec Realtek ALC298               |
| **Teclado**           | Teclado retroiluminado, layout ABNT2 (BR) ou US, sensor de digital integrado         |
| **Touchpad**          | Amplo, compatível com gestos Windows Precision                                      |
| **Leitor de Digital** | Integrado ao botão power, compatível com Windows Hello                             |
| **Dimensões**        | 355,4 x 250,4 x 16,5 mm                                                              |
| **Outros**            | Sensor de luz ambiente, suporte a Samsung Multi Control, Second Screen, Quick Share  |
| **Certificações**   | ENERGY STAR, RoHS, FCC, CE                                                           |

* [Manual do Usuário](https://downloadcenter.samsung.com/content/UM/202506/20250618100447559/94xXGK_96xXGK_96xXGL_Win11_UM_ENG_Rev.2.0_250602.pdf)
* [Especificações](https://www.samsung.com/br/support/model/NP960XGL-XG1BR/)

> [!TIP]
> **Kernel recomendado:** Utilize o kernel **6.15 ou superior** para garantir o reconhecimento adequado do hardware do Galaxy Book4 Ultra. Versões anteriores apresentam limitações significativas de compatibilidade.
>
> Caso você não seja um usuário avançado, recomendo que use o Windows 11, que já vem instalado de fábrica e funciona perfeitamente neste modelo.

---

## Índice

- [Fedora no Samsung Galaxy Book4 Ultra](#fedora-no-samsung-galaxy-book4-ultra)
  - [Índice](#índice)
  - [Repositórios Dedicados](#repositórios-dedicados)
  - [Incompatibilidades](#incompatibilidades)
  - [Alto-falantes Internos (Realtek ALC298)](#alto-falantes-internos-realtek-alc298)
  - [Câmera Interna (IPU6/OV02C10)](#câmera-interna-ipu6ov02c10)
  - [Leitor de Digital (Fingerprint)](#leitor-de-digital-fingerprint)
  - [Chip de Gráficos dedicados (NVIDIA RTX 4070)](#chip-de-gráficos-dedicados-nvidia-rtx-4070)
  - [Diagnósticos Rápidos](#diagnósticos-rápidos)
  - [Outros Relatos](#outros-relatos)

---

## Repositórios Dedicados

O trabalho deste notebook foi dividido em repositórios dedicados para webcam, áudio e instalação:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  Driver `ov02c10` ajustado e empacotado como `akmod` para Fedora.
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  App de câmera com UI nativa do GNOME em `GTK4` e `libadwaita`, usando `libcamera`.
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  Auxiliar de instalação e diagnóstico com interface gráfica para câmera, webcam em navegadores/comunicadores, NVIDIA, perfil de uso `balanced` da plataforma e integrações do GNOME.
- [`fedora-galaxy-book-max98390`](https://github.com/regiscaio/fedora-galaxy-book-max98390)
  Suporte `MAX98390` empacotado como `akmod` para os amplificadores dos alto-falantes internos.

Esses quatro projetos consolidam a linha atual de webcam, áudio e diagnóstico no Galaxy Book4 Ultra e reduzem a necessidade de repetir scripts e workarounds soltos dentro deste guia principal.

### Resumo da solução atual da câmera

O fluxo que faz mais sentido hoje para quem quer usar a webcam no Fedora é:

1. instalar o driver `ov02c10` empacotado em `fedora-galaxy-book-ov02c10`;
2. instalar o app `Galaxy Book Câmera`, com UI nativa do GNOME;
3. usar o `Galaxy Book Setup` para diagnóstico, reparo do stack Intel IPU6 e exposição da câmera para navegador e comunicadores.

Em outras palavras: este repositório principal continua como **guia geral do notebook**, enquanto a manutenção ativa da webcam passou a viver nos três projetos dedicados acima.

### Por que existe um app dedicado de câmera

O app nativo de câmera do GNOME foi uma referência importante de design e de
integração com o desktop, mas o caso do Galaxy Book4 Ultra exigiu um caminho
mais controlado.

Na prática, o problema não era só “abrir a câmera”:

- o sensor `OV02C10` exigiu um driver ajustado;
- o stack Intel IPU6 precisou ser tratado como parte da solução;
- o caminho direto do `libcamera` precisou de tuning próprio;
- a exposição da webcam para navegador e comunicadores precisou de bridge V4L2
  separada em alguns cenários.

Por isso a solução foi dividida em três frentes:

- um repositório para o driver;
- um app de câmera com UI nativa do GNOME para o fluxo principal de preview, foto e vídeo;
- um assistente gráfico para instalação, reparo e compatibilidade com o
  restante do sistema.

Ou seja: o objetivo não foi abandonar o app nativo do Fedora por preferência,
e sim criar uma solução dedicada para um hardware que, neste caso, precisava de
mais do que o fluxo genérico do desktop.

Hoje, o app nativo de câmera do Fedora/GNOME já pode funcionar neste notebook,
o que é um sinal claro de que o stack geral do sistema ficou mais saudável. Isso
não quer dizer, porém, que ele entregue a mesma experiência do `Galaxy Book
Câmera`.

Na prática, a diferença visual atual é esta:

- o app nativo do Fedora tende a mostrar uma imagem mais processada, com cor e
  balanço de branco mais agradáveis logo no padrão;
- o `Galaxy Book Câmera` usa um caminho direto via `libcamera`, visualmente
  mais cru e mais próximo do sensor, preservando mais detalhe fino e oferecendo
  muito mais controle sobre a imagem.

Em outras palavras: hoje os dois caminhos podem funcionar, mas eles servem a
prioridades diferentes. O app nativo do Fedora já é um bom sinal de
compatibilidade do sistema, enquanto o `Galaxy Book Câmera` continua sendo a
melhor opção quando a prioridade é detalhe, tuning do sensor e flexibilidade de
ajuste.

### Interfaces atuais

#### Galaxy Book Câmera

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-camera-icon.svg" alt="Ícone do Galaxy Book Câmera" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Câmera</strong><br>
      App de câmera com UI nativa do GNOME para o fluxo principal de preview, foto e vídeo no Galaxy Book4 Ultra.<br>
      Repositório: <a href="https://github.com/regiscaio/fedora-galaxy-book-camera">github.com/regiscaio/fedora-galaxy-book-camera</a>
    </td>
  </tr>
</table>

Tela principal:

![Galaxy Book Câmera — tela principal](img/app-camera-galaxy.png)

Modal `Sobre`:

![Galaxy Book Câmera — Sobre](img/app-camera-galaxy-about.png)

#### Galaxy Book Setup

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-setup-icon.svg" alt="Ícone do Galaxy Book Setup" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Setup</strong><br>
      Auxiliar gráfico de instalação, reparo e diagnóstico para câmera, navegador/comunicadores, NVIDIA e integrações do desktop.<br>
      Repositório: <a href="https://github.com/regiscaio/fedora-galaxy-book-setup">github.com/regiscaio/fedora-galaxy-book-setup</a>
    </td>
  </tr>
</table>

Tela inicial:

![Galaxy Book Setup — tela inicial](img/app-setup-galaxy.png)

Tela de diagnósticos:

![Galaxy Book Setup — diagnósticos](img/app-setup-galaxy-diagnostic.png)

Modal `Sobre`:

![Galaxy Book Setup — Sobre](img/app-setup-galaxy-about.png)

---

## Incompatibilidades


| Componente            | Status revalidado em abril de 2026 | Causa técnica resumida                                                                   |
| ----------------------- | :---------------------: | ------------------------------------------------------------------------------------------- |
| **Áudio interno**    | Parcial com workaround local | O bug upstream do ALSA continua relevante, mas o sistema atual já expõe `Speaker` e microfones via quirks locais do `snd-hda-intel` |
| **Câmera interna**   | Funciona com solução dedicada | Requer driver `ov02c10` ajustado, empacotamento `akmod` e app com UI nativa do GNOME mantidos em repositórios separados |
| **Leitor de digital** |   Parcial/instável   | O `fprintd` detecta o sensor Egis, mas cadastro e comportamento pós-suspensão ainda precisam de validação contínua |
| **NVIDIA RTX 4070**   | Funciona c/ ressalvas | Os módulos proprietários carregam, inclusive com Secure Boot ativo, mas updates e ferramentas de verificação ainda exigem atenção |
| **BIOS/Firmware**     |    Riscos recentes    | Updates recentes causam throttling, falhas em docks e Bluetooth                           |

---

## Alto-falantes Internos (Realtek ALC298)

O Galaxy Book4 Ultra usa o codec **Realtek ALC298**. No estado atual do meu notebook, o Linux já expõe `Speaker`, `Stereo Microphone` e `Digital Microphone`. O áudio interno deixou de ser um caso de “não funciona” e passou a ser um caso de **funciona com quirk local, mas ainda não está resolvido upstream**.

> [!IMPORTANT]
> Relatei no [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220363), que o kernel associa o hardware e, em logs avançados (anexo `alsa-info.log`), é possível ver o modelo do codec, mas a ausência de roteamento adequado (“speaker_outs=0”) no fixup do ALSA, impede o funcionamento do canal de alto-falantes internos. Nessa situação, apenas correções no driver/ALSA resolverá.
>
> No próprio bug, há também uma resposta de **Zhang Heng em 23 de dezembro de 2025** dizendo que o mapeamento do speaker em `0x17` parece normal e sugerindo testar três modelos no kernel:
>
> - `alc298-samsung-amp-v2-2-amps`
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
>
> Ele também sugere comparar o dump do codec com o Windows caso nenhum deles funcione.
>
> Em abril de 2026, esse contexto **continua útil como explicação upstream**, mas o estado prático do sistema já não é exatamente o mesmo descrito em 2025. Hoje, minha configuração funcional depende destes dois parâmetros no `snd-hda-intel`:
>
> - `model=alc298-samsung-amp-v2-2-amps`
> - `patch=alc298-internal-amp.fw`
>
> Isso é relevante porque, em **5 de abril de 2026**, eu já estava justamente usando `alc298-samsung-amp-v2-2-amps`, ou seja, uma das alternativas sugeridas no próprio bug.
>
> Em outras palavras: o problema **não está limpo/upstream**, mas também já não é mais correto resumir o estado atual apenas como “não funciona”.

![alt text](img/settings-audio.png)

### Configuração atual do áudio

Hoje, o que faz sentido manter é a configuração do próprio driver:

```bash
sudo tee /etc/modprobe.d/alc298.conf <<< 'options snd-hda-intel patch=alc298-internal-amp.fw'
sudo tee /etc/modprobe.d/audio-fix.conf <<< 'options snd-hda-intel model=alc298-samsung-amp-v2-2-amps'
sudo reboot
```

### Verificação

```bash
wpctl status
cat /sys/module/snd_hda_intel/parameters/model
cat /sys/module/snd_hda_intel/parameters/patch
speaker-test -c 2 -t wav
```

> [!CAUTION]
> Métodos que eu **não** considero mais parte da solução atual:
>
> - `speaker-init.service`
> - `samsung-audio-fix.service`
> - scripts de inicialização com `hda-verb`
>
> Esses caminhos ficaram legados no meu setup. Em abril de 2026, os services antigos de áudio estavam falhando no boot e já não definiam mais o comportamento real do sistema.
>
> Se esse quirk parar de funcionar, os próximos testes coerentes continuam sendo:
>
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
> - comparar o dump do codec com o Windows

---

## Câmera Interna (IPU6/OV02C10)

A câmera interna do Galaxy Book4 Ultra **passou a funcionar com uma solução dedicada**, composta por um driver `ov02c10` ajustado, um app de câmera com UI nativa do GNOME e um assistente gráfico de instalação e diagnóstico.

> [!IMPORTANT]
> O relato upstream no [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220364) continua relevante para explicar a falha original do sensor OmniVision OV02C10. O problema base segue sendo o clock externo de `26 MHz`, incompatível com o caminho in-tree do driver que esperava `19.2 MHz`.
>
> Quando o sistema cai de volta no driver in-tree, o erro continua sendo este:
>
> ```text
> external clock 26000000 is not supported
> probe with driver ov02c10 failed with error -22
> ```
>
> A diferença é que agora existe um caminho funcional mantido fora deste guia principal.

![Galaxy Book Câmera — fluxo principal](img/app-camera-galaxy.png)

### Solução atual

Os repositórios que concentram essa solução são:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  mantém o módulo `ov02c10` alinhado ao stack Intel IPU6 do Fedora, o empacotamento `akmod` e o carregamento automático do driver no boot;
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  entrega o app de câmera com UI nativa do GNOME para uso diário;
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  organiza diagnósticos e ações rápidas para instalação, reparo, ajuste de prioridade do driver, restauração do stack Intel IPU6 e ativação da webcam V4L2 para navegadores e comunicadores, além de acompanhar NVIDIA, perfil `balanced` e integrações do desktop.

O trabalho nesses repositórios parte dos aprendizados do fix comunitário:

- <https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix/>

Hoje, se eu preciso da webcam no Fedora, o caminho recomendado não é mais insistir em workarounds manuais antigos dentro deste repositório, e sim usar o driver dedicado e o app com UI nativa do GNOME mantidos nos repositórios acima.

> [!NOTE]
> Ao atualizar os RPMs locais do driver fora de um repositório, inclua juntos
> `galaxybook-ov02c10-kmod-common`, `akmod-galaxybook-ov02c10` e
> `kmod-galaxybook-ov02c10`. Um metapacote `kmod-galaxybook-ov02c10` antigo
> pode prender a versão anterior do `akmod` e fazer o `dnf` ignorar a
> atualização do módulo corrigido.

Na prática, o `Galaxy Book Setup` passou a ser o ponto central para:

- instalar o conjunto da câmera;
- reconstruir o driver com `akmods`;
- ajustar a prioridade do módulo corrigido quando o sistema cai no `ov02c10` in-tree;
- validar a câmera no `libcamera`;
- expor a câmera interna como webcam V4L2 para Meet, Teams, Discord e outros apps WebRTC;
- acompanhar também o estado da NVIDIA, o perfil `balanced` da plataforma e extensões úteis do GNOME.

### O que não faz mais sentido usar

Os seguintes caminhos ficaram legados e **não** representam mais a solução atual da câmera base:

- `modprobe ov02c10 clock_frequency=19200000`
- `ov02c10-clock-fix.service`
- usar `v4l2-relayd` + `icamerasrc` como forma de “provar” a detecção principal do sensor

O antigo experimento com `clock_frequency=19200000` ficou obsoleto. O módulo atual responde:

```text
ov02c10: unknown parameter 'clock_frequency' ignored
```

### Verificação

```bash
modinfo -n ov02c10
journalctl -b -k | grep -i ov02c10
cam -l
journalctl -b -u akmods --no-pager
```

> [!CAUTION]
> **Erro comum quando o sistema voltou ao driver in-tree**:
> ```external clock 26000000 is not supported ``` \
> ```probe with driver ov02c10 failed with error -22```
>
> Isso indica que o módulo corrigido não foi carregado no boot e que o sistema caiu de volta na cópia in-tree do kernel.
>
> Nessa situação, o caminho recomendado é:
>
> - abrir o `Galaxy Book Setup`;
> - revisar o status do `akmods` e do módulo ativo;
> - ou consultar o README do `fedora-galaxy-book-ov02c10` para o fluxo de reparo.

> [!NOTE]
> O bridge com `v4l2-relayd`, `icamerasrc` e `v4l2loopback` continua sendo válido, mas agora com outro papel: **compatibilidade com navegadores e comunicadores**. Ele não substitui a validação do `libcamera`; ele entra depois, para expor a câmera como webcam V4L2 para apps WebRTC.

---

## Leitor de Digital (Fingerprint)

O leitor de digital deste notebook é um **Egis / LighTuning ETU905A80-E** (`1c7a:05a1`).

Em **20 de abril de 2026**, o cenário local ficou assim:

- o sensor apareceu no `lsusb` como `1c7a:05a1 LighTuning Technology Inc. ETU905A80-E`;
- o `fprintd` subiu normalmente;
- o perfil `authselect` já estava com `with-fingerprint` habilitado;
- o `libfprint` instalado era o `1.94.10`, que já inclui o driver `egismoc` usado por esse sensor.

Ou seja: neste ponto, o leitor **não está no cenário de hardware sem suporte**. O que ainda faltava validar era o uso diário com digitais realmente cadastradas, além do comportamento após suspensão.

![alt text](img/settings-fingerprint.png)

### Fluxo recomendado

Se o sensor aparecer, mas o cadastro ou a autenticação falharem, o caminho mais seguro continua sendo reinstalar o stack e refazer o cadastro:

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
lsusb | grep -i '1c7a:05a1'
fprintd-list $USER
authselect current
fprintd-verify
```

> [!CAUTION]
> **Erro comum**:
> ```Failed to enroll fingerprint: Device or resource busy```
>
> Por algum motivo, o sensor pode estar ocupado ou não configurado corretamente na inicialização. Como consequência, não consegue localizar a digital cadastrada anteriormente e utilizá-la para autenticação.

---

## Chip de Gráficos dedicados (NVIDIA RTX 4070)

Funciona bem com RPM Fusion, mas requer atenção com updates de kernel e Secure Boot com processo de MOK.

Em abril de 2026, os módulos `nvidia`, `nvidia_modeset`, `nvidia_drm` e `nvidia_uvm` estavam carregados com **Secure Boot habilitado**. Ou seja: o driver em si estava funcional.

Também vale separar duas coisas que costumam ser tratadas como se fossem a mesma:

- driver NVIDIA carregado;
- utilitário `nvidia-smi` instalado.

No meu caso, o driver estava funcionando mesmo sem o binário `nvidia-smi` no `PATH`. No Fedora/RPM Fusion, esse binário vem do pacote `xorg-x11-drv-nvidia-cuda`, então ele deve ser tratado como **ferramenta opcional de administração/diagnóstico**, não como pré-requisito para o driver existir.

![alt text](img/settings-gpu.png)

### Solução: Instalação do driver NVIDIA

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf update
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
sudo reboot
```

Se a intenção for **só** ter o driver funcionando, o ponto principal é o `akmod-nvidia`.

Se a intenção for também ter o `nvidia-smi`, então faz sentido manter explicitamente o `xorg-x11-drv-nvidia-cuda` instalado.

Se falhar após update:

```bash
sudo akmods --force
sudo dracut --force
sudo reboot
```

Desative Secure Boot se for necessário.

### Verificação

Para verificar o **driver**:

```bash
lsmod | grep nvidia
mokutil --sb-state
```

Para verificar o **utilitário `nvidia-smi`**:

```bash
nvidia-smi
```

> [!CAUTION]
> `nvidia-smi` não deve ser o único critério para avaliar se o driver está funcionando.
>
> Se o comando nem existir, isso normalmente indica ausência do pacote `xorg-x11-drv-nvidia-cuda`, não ausência do driver.
>
> **Erro comum**:
> ```Unable to determine the device handle for GPU0: 0000:01:00.0: Unknown Error``` \
> ```No devices were found```
>
> O driver NVIDIA não está carregado corretamente ou o Secure Boot está bloqueando o módulo. Isso acontece a cada atualização de kernel, então pode ser necessário reinstalar o driver e configurar. Apesar de prático, ao optar por desativar o Secure Boot você perde a segurança adicional que ele oferece, então faça por sua conta e risco.

---

## Diagnósticos Rápidos

| Componente  | Comando de Diagnóstico                                                                                              |
| :---------- | :------------------------------------------------------------------------------------------------------------------ |
| **Kernel**  | `uname -r`                                                                                                          |
| **Áudio**   | `aplay -l && wpctl status && cat /sys/module/snd_hda_intel/parameters/model && cat /sys/module/snd_hda_intel/parameters/patch` |
| **Câmera**  | `modinfo -n ov02c10 && journalctl -b -k \| grep -i ov02c10 && cam -l && journalctl -b -u akmods --no-pager` |
| **Digital** | `fprintd-list $USER && systemctl status fprintd.service --no-pager && journalctl -b \| grep -i fprint` |
| **NVIDIA**  | `lsmod \| grep nvidia && mokutil --sb-state && rpm -qa \| grep -i nvidia` |

## Outros Relatos

> [!NOTE]
> Estes links contêm relatos e experiências de outros usuários com o Galaxy Book4 Ultra no linux, abordando desde problemas de hardware até tentativas de soluções criativas.

- [Relato de tentativa de engenharia reversa para alto-falantes internos no EndeavourOS](https://github.com/dgunay/galaxy-book4-pro-reverse-engineering)
- [Relato com uso do Fedora 42 (KDE) com medições de desempenho da Intel Xe Iris](https://github.com/jusqua/galaxy-book4-linux)
- [Fix comunitário do Galaxy Book3 para o driver OV02C10 da câmera](https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix)
