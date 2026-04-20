<p>
  <a href="README.md">🇧🇷 Português</a>
  <a href="README.en.md">🇺🇸 English</a>
  <a href="README.es.md">🇪🇸 Español</a>
  <a href="README.it.md">🇮🇹 Italiano</a>
</p>

# Fedora sul Samsung Galaxy Book4 Ultra

## Installazione rapida

Per installare la linea attuale del supporto principale del notebook tramite repository DNF:

```bash
sudo dnf config-manager addrepo --from-repofile=https://packages.caioregis.com/fedora/caioregis.repo
sudo dnf install galaxybook-camera galaxybook-setup akmod-galaxybook-ov02c10 akmod-galaxybook-max98390
```

Questo flusso installa:

- l'app fotocamera con UI nativa di GNOME;
- l'assistente grafico per installazione e diagnosi;
- il driver `ov02c10` impacchettato come `akmod`;
- il supporto `MAX98390` impacchettato come `akmod` per gli altoparlanti interni.

> [!IMPORTANT]
> **Aggiornato il 20 aprile 2026**
>
> Questa guida raccoglie spiegazioni tecniche, soluzioni pratiche e informazioni utili per gli utenti Fedora sul Samsung Galaxy Book4 Ultra, con focus su audio, fotocamera, lettore di impronte e driver NVIDIA.
>
> Il contenuto storico qui sotto è stato **riconvalidato il 5 aprile 2026** su un **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, in esecuzione con **Fedora 43** e kernel **6.18.8-200.fc43.x86_64**.
>
> A partire dal **19 aprile 2026**, la soluzione della fotocamera e il supporto dedicato agli altoparlanti interni sono stati mantenuti in repository separati, con driver, app fotocamera con UI nativa di GNOME, assistente di installazione e driver audio distinti. Questo README continua a fungere da guida generale del notebook e rimanda a questi progetti quando si parla di webcam e audio.
>
> La linea attuale dei progetti dedicati è stata **verificata il 20 aprile 2026** su un **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, in esecuzione con **Fedora 44** e kernel **6.19.10-300.fc44.x86_64**, nelle seguenti versioni:
>
> - `fedora-galaxy-book-ov02c10`: **1.0.0-1**
> - `fedora-galaxy-book-camera`: **1.0.0-2**
> - `fedora-galaxy-book-setup`: **1.0.0-4**
> - `fedora-galaxy-book-max98390`: **1.0.0-1**

| Specifica             | Dettagli                                                                           |
| --------------------- | :---------------------------------------------------------------------------------- |
| **Modello**           | Samsung Galaxy Book4 Ultra (NP960XGL, NP960XGL-XG1BR, NP960XGLZ-EXP...)            |
| **Schermo**           | 16" WQXGA+ (2880 x 1800) AMOLED Touchscreen                                          |
| **Processore**        | Intel Core Ultra 9 185H (Meteor Lake, 16C/22T, fino a 5.1 GHz, 24 MB di cache)     |
| **GPU**               | NVIDIA GeForce RTX 4070 Laptop 8 GB GDDR6                                            |
| **RAM**               | 32 GB LPDDR5X (saldati, non espandibili)                                              |
| **Archiviazione**     | 1 TB SSD NVMe PCIe Gen4 (espandibile tramite slot M.2 secondario, a seconda del modello) |
| **Batteria**          | 76 Wh (integrata, agli ioni di litio)                                                |
| **Caricatore**        | 140 W USB-C (20V, 5A oppure 28V, 5A)                                                 |
| **Porte**             | 2x Thunderbolt 4 (USB-C), 1x HDMI 2.1, 1x USB-A 3.2, 1x jack P2 audio, 1x MicroSD    |
| **Rete wireless**     | Wi-Fi 6E (802.11ax), Bluetooth 5.3                                                  |
| **Webcam**            | 1080p FHD, sensore OmniVision OV02C10, IPU6 (MIPI)                                   |
| **Audio**             | 2x altoparlanti stereo (AKG), doppio microfono, codec Realtek ALC298                |
| **Tastiera**          | Tastiera retroilluminata, layout ABNT2 (BR) oppure US, sensore di impronte integrato |
| **Touchpad**          | Ampio, compatibile con i gesti Windows Precision                                     |
| **Lettore di impronte** | Integrato nel pulsante di accensione, compatibile con Windows Hello                 |
| **Dimensioni**        | 355,4 x 250,4 x 16,5 mm                                                              |
| **Altro**             | Sensore di luminosità ambientale, supporto a Samsung Multi Control, Second Screen, Quick Share |
| **Certificazioni**     | ENERGY STAR, RoHS, FCC, CE                                                           |

* [Manuale dell'utente](https://downloadcenter.samsung.com/content/UM/202506/20250618100447559/94xXGK_96xXGK_96xXGL_Win11_UM_ENG_Rev.2.0_250602.pdf)
* [Specifiche](https://www.samsung.com/br/support/model/NP960XGL-XG1BR/)

> [!TIP]
> **Kernel consigliato:** usa il kernel **6.15 o superiore** per garantire il riconoscimento corretto dell'hardware del Galaxy Book4 Ultra. Le versioni precedenti presentano limitazioni significative di compatibilità.
>
> Se non sei un utente esperto, ti consiglio di usare Windows 11, che è già installato di fabbrica e funziona perfettamente su questo modello.

---

## Indice

- [Fedora sul Samsung Galaxy Book4 Ultra](#fedora-sul-samsung-galaxy-book4-ultra)
  - [Installazione rapida](#installazione-rapida)
  - [Indice](#indice)
  - [Repository dedicati](#repository-dedicati)
    - [Riepilogo della soluzione attuale della fotocamera](#riepilogo-della-soluzione-attuale-della-fotocamera)
    - [Perché esiste un'app fotocamera dedicata](#perché-esiste-unapp-fotocamera-dedicata)
    - [Interfacce attuali](#interfacce-attuali)
      - [Galaxy Book Câmera](#galaxy-book-câmera)
      - [Galaxy Book Setup](#galaxy-book-setup)
  - [Stato attuale](#stato-attuale)
  - [Altoparlanti interni (Realtek ALC298)](#altoparlanti-interni-realtek-alc298)
    - [Flusso consigliato](#flusso-consigliato)
    - [Verifica](#verifica)
  - [Fotocamera interna (IPU6/OV02C10)](#fotocamera-interna-ipu6ov02c10)
    - [Soluzione attuale](#soluzione-attuale)
    - [Cosa non ha più senso usare](#cosa-non-ha-più-senso-usare)
    - [Verifica](#verifica-1)
  - [Lettore di impronte (Fingerprint)](#lettore-di-impronte-fingerprint)
    - [Flusso consigliato](#flusso-consigliato-1)
    - [Verifica](#verifica-2)
  - [Chip grafico dedicato (NVIDIA RTX 4070)](#chip-grafico-dedicato-nvidia-rtx-4070)
    - [Soluzione: installazione del driver NVIDIA](#soluzione-installazione-del-driver-nvidia)
    - [Verifica](#verifica-3)
  - [Diagnostica rapida](#diagnostica-rapida)
  - [Altri resoconti](#altri-resoconti)

---

## Repository dedicati

Il lavoro su questo notebook è stato suddiviso in repository dedicati per webcam, audio e installazione:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  Driver `ov02c10` adattato e impacchettato come `akmod` per Fedora.
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  App fotocamera con UI nativa di GNOME in `GTK4` e `libadwaita`, che usa `libcamera`.
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  Assistente grafico di installazione e diagnosi per fotocamera, webcam in browser/comunicazioni, NVIDIA, profilo di utilizzo `balanced` della piattaforma e integrazioni GNOME.
- [`fedora-galaxy-book-max98390`](https://github.com/regiscaio/fedora-galaxy-book-max98390)
  Supporto `MAX98390` impacchettato come `akmod` per gli amplificatori degli altoparlanti interni.

Questi quattro progetti consolidano la linea attuale di webcam, audio e diagnosi sul Galaxy Book4 Ultra e riducono la necessità di ripetere script e workaround sparsi dentro questa guida principale.

### Riepilogo della soluzione attuale della fotocamera

Il flusso che oggi ha più senso per chi vuole usare la webcam su Fedora è:

1. installare il driver `ov02c10` impacchettato in `fedora-galaxy-book-ov02c10`;
2. installare l'app `Galaxy Book Câmera`, con UI nativa di GNOME;
3. usare `Galaxy Book Setup` per la diagnosi, il ripristino dello stack Intel IPU6 e l'esposizione della fotocamera a browser e applicazioni di comunicazione.

In altre parole: questo repository principale continua a essere la **guida generale del notebook**, mentre la manutenzione attiva della webcam è passata ai tre progetti dedicati sopra.

### Perché esiste un'app fotocamera dedicata

L'app fotocamera nativa di GNOME è stata un riferimento importante per il design e l'integrazione con il desktop, ma il caso del Galaxy Book4 Ultra ha richiesto un percorso più controllato.

In pratica, il problema non era solo "aprire la fotocamera":

- il sensore `OV02C10` ha richiesto un driver adattato;
- lo stack Intel IPU6 ha dovuto essere trattato come parte della soluzione;
- il percorso diretto di `libcamera` ha richiesto una taratura propria;
- l'esposizione della webcam per browser e applicazioni di comunicazione ha richiesto in alcuni scenari un bridge V4L2 separato.

Per questo la soluzione è stata divisa in tre fronti:

- un repository per il driver;
- un'app fotocamera con UI nativa di GNOME per il flusso principale di anteprima, foto e video;
- un assistente grafico per installazione, riparazione e compatibilità con il resto del sistema.

In altre parole: l'obiettivo non è stato abbandonare l'app nativa di Fedora per preferenza, ma creare una soluzione dedicata per un hardware che, in questo caso, richiedeva più del flusso generico del desktop.

Oggi, l'app fotocamera nativa di Fedora/GNOME può già funzionare su questo notebook, il che è un segnale chiaro che lo stack generale del sistema è diventato più sano. Questo non significa però che offra la stessa esperienza di `Galaxy Book Câmera`.

In pratica, la differenza visiva attuale è questa:

- l'app nativa di Fedora tende a mostrare un'immagine più elaborata, con colore e bilanciamento del bianco più gradevoli già di default;
- `Galaxy Book Câmera` usa un percorso diretto via `libcamera`, visivamente più grezzo e più vicino al sensore, preservando più dettaglio fine e offrendo molto più controllo sull'immagine.

In altre parole: oggi entrambi i percorsi possono funzionare, ma servono priorità diverse. L'app nativa di Fedora è già un buon segnale di compatibilità del sistema, mentre `Galaxy Book Câmera` resta la migliore opzione quando la priorità è il dettaglio, la taratura del sensore e la flessibilità di regolazione.

### Interfacce attuali

#### Galaxy Book Câmera

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-camera-icon.svg" alt="Icona di Galaxy Book Câmera" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Câmera</strong><br>
      App fotocamera con UI nativa di GNOME per il flusso principale di anteprima, foto e video sul Galaxy Book4 Ultra.<br>
      Repository: <a href="https://github.com/regiscaio/fedora-galaxy-book-camera">github.com/regiscaio/fedora-galaxy-book-camera</a>
    </td>
  </tr>
</table>

Schermata principale:

![Galaxy Book Câmera — schermata principale](img/app-camera-galaxy.png)

Finestra modale `Informazioni`:

![Galaxy Book Câmera — Informazioni](img/app-camera-galaxy-about.png)

#### Galaxy Book Setup

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-setup-icon.svg" alt="Icona di Galaxy Book Setup" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Setup</strong><br>
      Assistente grafico di installazione, riparazione e diagnosi per fotocamera, browser/comunicazioni, NVIDIA e integrazioni desktop.<br>
      Repository: <a href="https://github.com/regiscaio/fedora-galaxy-book-setup">github.com/regiscaio/fedora-galaxy-book-setup</a>
    </td>
  </tr>
</table>

Schermata iniziale:

![Galaxy Book Setup — schermata iniziale](img/app-setup-galaxy.png)

Schermata diagnostica:

![Galaxy Book Setup — diagnostica](img/app-setup-galaxy-diagnostic.png)

Finestra modale `Informazioni`:

![Galaxy Book Setup — Informazioni](img/app-setup-galaxy-about.png)

---

## Stato attuale


| Componente            | Stato riconvalidato ad aprile 2026 | Lettura pratica attuale                                                                                                                                                         |
| :-------------------- | :--------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Audio interno**     |              Funziona              | Il percorso attuale degli altoparlanti interni funziona già con la linea dedicata `MAX98390`, impacchettata in `fedora-galaxy-book-max98390` e integrata in `fedora-galaxy-book-setup`. |
| **Fotocamera interna** |              Funziona              | Lo stack attuale consente già di usare la fotocamera sia nell'app nativa di Fedora sia nella soluzione dedicata, con `ov02c10` impacchettato, `Galaxy Book Câmera` e `Galaxy Book Setup`. |
| **Lettore di impronte** |          Parziale/instabile        | `fprintd` rileva il sensore Egis, ma la registrazione persistente e il comportamento dopo la sospensione richiedono ancora una validazione continua.                            |
| **NVIDIA RTX 4070**   |       Funziona con riserve          | I moduli proprietari si caricano, anche con Secure Boot attivo, ma gli aggiornamenti del kernel e gli strumenti di verifica richiedono ancora attenzione.                        |
| **BIOS/Firmware**     |          Rischi recenti             | Gli aggiornamenti recenti continuano a essere associati a throttling, problemi con dock e regressioni Bluetooth.                                                                 |

---

## Altoparlanti interni (Realtek ALC298)

Oggi, gli **altoparlanti interni funzionano già** sul Galaxy Book4 Ultra tramite il percorso dedicato che combina il codec **Realtek ALC298** con il supporto agli amplificatori `MAX98390`.

In pratica, l'audio interno non dipende più da un insieme di quirks sparsi nel README stesso ed è stato spostato in repository dedicati:

- [`fedora-galaxy-book-max98390`](https://github.com/regiscaio/fedora-galaxy-book-max98390)
  concentra il supporto impacchettato in `RPM/akmod` per il percorso `MAX98390`;
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  organizza la diagnostica e le azioni rapide per il percorso degli altoparlanti interni.

In altre parole: l'audio interno è uscito dallo stato di "indagine locale" ed è entrato in un flusso installabile e riproducibile su Fedora.

![alt text](img/settings-audio.png)

### Flusso consigliato

Oggi, il percorso che ha senso per l'audio è:

```bash
sudo dnf config-manager addrepo --from-repofile=https://packages.caioregis.com/fedora/caioregis.repo
sudo dnf install galaxybook-setup akmod-galaxybook-max98390
```

Poi:

1. apri `Galaxy Book Setup`;
2. rivedi la diagnostica `Altoparlanti interni`;
3. esegui l'azione `Attiva altoparlanti interni`;
4. verifica l'uscita `Speaker` nel sistema.

### Verifica

```bash
modinfo -n snd-hda-scodec-max98390
modinfo -n snd-hda-scodec-max98390-i2c
wpctl status
lsmod | grep snd_hda_scodec_max98390
speaker-test -c 2 -t wav
```

> [!IMPORTANT]
> Il resoconto su [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220363)
> continua a essere utile come storico tecnico del problema originale. Descrive
> uno stato in cui il kernel riconosceva l'hardware, ma il routing degli
> altoparlanti interni non era ancora chiuso in modo utilizzabile nello stack
> standard.
>
> Nello stesso bug c'è anche una risposta di **Zhang Heng del 23 dicembre 2025** che dice che il mapping dello speaker in `0x17` sembra normale e suggerisce di provare tre modelli nel kernel:
>
> - `alc298-samsung-amp-v2-2-amps`
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
>
> Suggerisce anche di confrontare il dump del codec con Windows se nessuno di essi funziona.
> In aprile 2026, questo contesto **resta utile come spiegazione upstream**,
> ma non descrive più lo stato attuale del notebook con la linea dedicata
> `MAX98390`. Prima di quella, la mia configurazione locale è arrivata a
> dipendere da questi due parametri in `snd-hda-intel`:
>
> - `model=alc298-samsung-amp-v2-2-amps`
> - `patch=alc298-internal-amp.fw`
>
> Questo è rilevante perché, il **5 aprile 2026**, stavo già usando proprio `alc298-samsung-amp-v2-2-amps`, cioè una delle alternative suggerite nel bug stesso.
>
> In altre parole: il problema non può ancora essere considerato "risolto nell'upstream puro", ma non è nemmeno corretto descrivere il notebook come se l'audio interno semplicemente non funzionasse.

> [!CAUTION]
> I metodi che **non** considero più parte del flusso principale:
>
> - `speaker-init.service`
> - `samsung-audio-fix.service`
> - script di inizializzazione con `hda-verb`
> - concentrare il troubleshooting audio solo su quirks locali di `snd-hda-intel` dentro questo README
>
> Questi percorsi sono diventati legacy nella mia configurazione. Nell'aprile 2026, i vecchi service audio fallivano al boot e non definivano più il comportamento reale del sistema.
>
> Il contesto di `ALC298` resta importante per upstream e per il confronto del
> comportamento con Windows, ma il percorso pratico per l'utente finale deve
> ora passare dal repository dedicato `MAX98390` e da `Galaxy Book Setup`.

> [!NOTE]
> Se è necessario testare il lato più storico del codec, i modelli sotto
> restano le varianti più coerenti per il confronto:
>
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
> - confrontare il dump del codec con Windows

---

## Fotocamera interna (IPU6/OV02C10)

La fotocamera interna del Galaxy Book4 Ultra **ha iniziato a funzionare con una soluzione dedicata**, composta da un driver `ov02c10` adattato, un'app fotocamera con UI nativa di GNOME e un assistente grafico di installazione e diagnosi.

> [!IMPORTANT]
> Il resoconto upstream su [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220364) continua a essere rilevante per spiegare il guasto originale del sensore OmniVision OV02C10. Il problema di base resta il clock esterno da `26 MHz`, incompatibile con il percorso in-tree del driver che si aspettava `19.2 MHz`.
>
> Quando il sistema ricade nel driver in-tree, l'errore continua a essere questo:
>
> ```text
> external clock 26000000 is not supported
> probe with driver ov02c10 failed with error -22
> ```
>
> La differenza è che ora esiste un percorso funzionante mantenuto fuori da questa guida principale.

![Galaxy Book Câmera — flusso principale](img/app-camera-galaxy.png)

### Soluzione attuale

I repository che concentrano questa soluzione sono:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  mantiene il modulo `ov02c10` allineato allo stack Intel IPU6 di Fedora, il pacchetto `akmod` e il caricamento automatico del driver al boot;
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  fornisce l'app fotocamera con UI nativa di GNOME per l'uso quotidiano;
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  organizza diagnostica e azioni rapide per installazione, riparazione, regolazione della priorità del driver, ripristino dello stack Intel IPU6 e attivazione della webcam V4L2 per browser e comunicazioni, oltre a seguire anche NVIDIA, il profilo `balanced` e le integrazioni desktop.

Il lavoro in questi repository parte dagli insegnamenti della correzione comunitaria:

- <https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix/>

Oggi, se mi serve la webcam su Fedora, il percorso consigliato non è più insistere con vecchi workaround manuali dentro questo repository, ma usare il driver dedicato e l'app con UI nativa di GNOME mantenuti nei repository sopra.

> [!NOTE]
> Quando aggiorni gli RPM locali del driver fuori da un repository, includi insieme
> `galaxybook-ov02c10-kmod-common`, `akmod-galaxybook-ov02c10` e
> `kmod-galaxybook-ov02c10`. Un vecchio metapacchetto `kmod-galaxybook-ov02c10`
> può bloccare la versione precedente di `akmod` e far sì che `dnf` ignori
> l'aggiornamento del modulo corretto.

In pratica, `Galaxy Book Setup` è diventato il punto centrale per:

- installare il set della fotocamera;
- ricostruire il driver con `akmods`;
- regolare la priorità del modulo corretto quando il sistema ricade sull'`ov02c10` in-tree;
- verificare la fotocamera in `libcamera`;
- esporre la fotocamera interna come webcam V4L2 per Meet, Teams, Discord e altre app WebRTC;
- seguire anche lo stato della NVIDIA, il profilo `balanced` della piattaforma e utili estensioni GNOME.

Oggi, vale la pena chiarire un punto che è già cambiato rispetto alle prime
iterazioni di questa indagine: l'**app fotocamera nativa di Fedora/GNOME può già
funzionare** su questo notebook nello stato attuale dello stack.

Questo però non rende `Galaxy Book Câmera` inutile. I due percorsi offrono
esperienze diverse:

- l'app nativa di Fedora tende a mostrare un'immagine più elaborata, con colore e
  bilanciamento del bianco più gradevoli già di default;
- `Galaxy Book Câmera` usa un percorso diretto via `libcamera`, visivamente
  più grezzo e più vicino al sensore, preservando più dettaglio fine e offrendo
  un controllo molto più flessibile sull'immagine.

In altre parole: oggi lo stack della fotocamera consente già una migliore
compatibilità con il desktop, ma l'app dedicata continua a essere la scelta
migliore quando la priorità è il dettaglio, la taratura del sensore e il
controllo dell'immagine.

### Cosa non ha più senso usare

I percorsi seguenti sono diventati legacy e **non** rappresentano più la soluzione attuale di base della fotocamera:

- `modprobe ov02c10 clock_frequency=19200000`
- `ov02c10-clock-fix.service`
- usare `v4l2-relayd` + `icamerasrc` come modo per "provare" il rilevamento principale del sensore

Il vecchio esperimento con `clock_frequency=19200000` è diventato obsoleto. Il modulo attuale risponde:

```text
ov02c10: unknown parameter 'clock_frequency' ignored
```

### Verifica

```bash
modinfo -n ov02c10
journalctl -b -k | grep -i ov02c10
cam -l
journalctl -b -u akmods --no-pager
```

> [!CAUTION]
> **Errore comune quando il sistema è tornato al driver in-tree**:
> ```external clock 26000000 is not supported ``` \
> ```probe with driver ov02c10 failed with error -22```
>
> Questo indica che il modulo corretto non è stato caricato al boot e che il sistema è ricaduto nella copia in-tree del kernel.
>
> In questa situazione, il percorso consigliato è:
>
> - aprire `Galaxy Book Setup`;
> - verificare lo stato di `akmods` e del modulo attivo;
> - oppure consultare il README di `fedora-galaxy-book-ov02c10` per il flusso di riparazione.

> [!NOTE]
> Il bridge con `v4l2-relayd`, `icamerasrc` e `v4l2loopback` resta valido, ma
> ora ha un altro ruolo: **compatibilità con browser e comunicazioni**. Non
> sostituisce la verifica di `libcamera`; interviene dopo, per esporre la
> fotocamera come webcam V4L2 per app WebRTC.

---

## Lettore di impronte (Fingerprint)

Il lettore di impronte di questo notebook è un **Egis / LighTuning ETU905A80-E** (`1c7a:05a1`).

Il **20 aprile 2026** lo scenario locale era il seguente:

- il sensore compariva in `lsusb` come `1c7a:05a1 LighTuning Technology Inc. ETU905A80-E`;
- `fprintd` si avviava normalmente;
- il profilo `authselect` aveva già `with-fingerprint` abilitato;
- `libfprint` installato era `1.94.10`, che include già il driver `egismoc` usato da questo sensore.

In altre parole: a questo punto, il lettore **non è nello scenario di hardware senza supporto**. Ciò che restava ancora da validare era l'uso quotidiano con impronte realmente registrate, oltre al comportamento dopo la sospensione.

![alt text](img/settings-fingerprint.png)

### Flusso consigliato

Se il sensore compare, ma la registrazione o l'autenticazione falliscono, il percorso più sicuro continua a essere reinstallare lo stack e rifare la registrazione:

```bash
sudo dnf reinstall fprintd libfprint
sudo systemctl restart fprintd
fprintd-delete $USER
fprintd-enroll
sudo authselect enable-feature with-fingerprint
sudo authselect apply-changes
```

### Verifica

```bash
lsusb | grep -i '1c7a:05a1'
fprintd-list $USER
authselect current
fprintd-verify
```

> [!CAUTION]
> **Errore comune**:
> ```Failed to enroll fingerprint: Device or resource busy```
>
> Per qualche motivo, il sensore può essere occupato o non configurato correttamente all'avvio. Di conseguenza, non riesce a trovare l'impronta registrata in precedenza e a usarla per l'autenticazione.

---

## Chip grafico dedicato (NVIDIA RTX 4070)

Funziona bene con RPM Fusion, ma richiede attenzione agli aggiornamenti di kernel e Secure Boot con il processo MOK.

In aprile 2026, i moduli `nvidia`, `nvidia_modeset`, `nvidia_drm` e `nvidia_uvm` erano caricati con **Secure Boot abilitato**. In altre parole: il driver in sé era funzionante.

Vale anche la pena distinguere due cose che vengono spesso trattate come se fossero la stessa:

- driver NVIDIA caricato;
- utility `nvidia-smi` installata.

Nel mio caso, il driver funzionava anche senza il binario `nvidia-smi` nel `PATH`. Su Fedora/RPM Fusion, questo binario arriva dal pacchetto `xorg-x11-drv-nvidia-cuda`, quindi va considerato come **strumento opzionale di amministrazione/diagnosi**, non come prerequisito per l'esistenza del driver.

![alt text](img/settings-gpu.png)

### Soluzione: installazione del driver NVIDIA

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf update
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
sudo reboot
```

Se l'intenzione è **solo** avere il driver funzionante, il punto principale è `akmod-nvidia`.

Se l'intenzione è anche avere `nvidia-smi`, allora ha senso mantenere esplicitamente installato `xorg-x11-drv-nvidia-cuda`.

Se fallisce dopo un aggiornamento:

```bash
sudo akmods --force
sudo dracut --force
sudo reboot
```

Disabilita Secure Boot se necessario.

### Verifica

Per verificare il **driver**:

```bash
lsmod | grep nvidia
mokutil --sb-state
```

Per verificare l'**utility `nvidia-smi`**:

```bash
nvidia-smi
```

> [!CAUTION]
> `nvidia-smi` non deve essere l'unico criterio per valutare se il driver funziona.
>
> Se il comando non esiste affatto, di solito indica l'assenza del pacchetto `xorg-x11-drv-nvidia-cuda`, non l'assenza del driver.
>
> **Errore comune**:
> ```Unable to determine the device handle for GPU0: 0000:01:00.0: Unknown Error``` \
> ```No devices were found```
>
> Il driver NVIDIA non è caricato correttamente oppure Secure Boot sta bloccando il modulo. Succede a ogni aggiornamento del kernel, quindi può essere necessario reinstallare il driver e configurarlo. Anche se pratico, scegliendo di disabilitare Secure Boot perdi la sicurezza aggiuntiva che offre, quindi fallo a tuo rischio e pericolo.

---

## Diagnostica rapida

| Componente  | Comando di diagnostica                                                                                                        |
| :---------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **Kernel**  | `uname -r`                                                                                                                    |
| **Audio**   | `aplay -l && wpctl status && cat /sys/module/snd_hda_intel/parameters/model && cat /sys/module/snd_hda_intel/parameters/patch` |
| **Fotocamera**  | `modinfo -n ov02c10 && journalctl -b -k \| grep -i ov02c10 && cam -l && journalctl -b -u akmods --no-pager`                   |
| **Impronte** | `fprintd-list $USER && systemctl status fprintd.service --no-pager && journalctl -b \| grep -i fprint`                        |
| **NVIDIA**  | `lsmod \| grep nvidia && mokutil --sb-state && rpm -qa \| grep -i nvidia`                                                     |

## Altri resoconti

> [!NOTE]
> Questi link contengono resoconti ed esperienze di altri utenti con il Galaxy Book4 Ultra su Linux, che vanno dai problemi hardware ai tentativi di soluzioni creative.

- [Resoconto di un tentativo di reverse engineering per gli altoparlanti interni su EndeavourOS](https://github.com/dgunay/galaxy-book4-pro-reverse-engineering)
- [Resoconto con uso di Fedora 42 (KDE) con misurazioni di prestazioni della Intel Xe Iris](https://github.com/jusqua/galaxy-book4-linux)
- [Fix comunitario del Galaxy Book3 per il driver OV02C10 della fotocamera](https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix)
