<p>
  <a href="README.md">🇧🇷 Português</a>
  <a href="README.en.md">🇺🇸 English</a>
  <a href="README.es.md">🇪🇸 Español</a>
  <a href="README.it.md">🇮🇹 Italiano</a>
</p>

# Fedora on the Samsung Galaxy Book4 Ultra

## Quick Install

To install the current mainline support stack for the notebook via the DNF repository:

```bash
sudo dnf config-manager addrepo --from-repofile=https://packages.caioregis.com/fedora/caioregis.repo
sudo dnf install galaxybook-camera galaxybook-setup akmod-galaxybook-ov02c10 akmod-galaxybook-max98390
```

This flow installs:

- the camera app with a native GNOME UI;
- the graphical installation and diagnostics assistant;
- the `ov02c10` driver packaged as an `akmod`;
- `MAX98390` support packaged as an `akmod` for the internal speakers.

> [!IMPORTANT]
> **Updated on April 20, 2026**
>
> This guide collects technical explanations, practical fixes, and useful information for Fedora users on the Samsung Galaxy Book4 Ultra, with a focus on audio, camera, fingerprint reader, and NVIDIA drivers.
>
> The historical content below was **revalidated on April 5, 2026** on a **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, running **Fedora 43** with kernel **6.18.8-200.fc43.x86_64**.
>
> Starting on **April 19, 2026**, the camera solution and the dedicated support for the internal speakers began to be maintained in separate repositories, with the driver, native GNOME camera app, installation assistant, and audio driver split apart. This README remains the general notebook guide and points to those projects when the topic is webcam or audio.
>
> The current line of the dedicated projects was **verified on April 20, 2026** on a **Samsung Galaxy Book4 Ultra NP960XGL-XG1BR**, running **Fedora 44** with kernel **6.19.10-300.fc44.x86_64**, with the following versions:
>
> - `fedora-galaxy-book-ov02c10`: **1.0.0-1**
> - `fedora-galaxy-book-camera`: **1.0.0-1**
> - `fedora-galaxy-book-setup`: **1.0.0-1**
> - `fedora-galaxy-book-max98390`: **1.0.0-1**

| Specification      | Details                                                                               |
| ------------------ | :------------------------------------------------------------------------------------ |
| **Model**          | Samsung Galaxy Book4 Ultra (NP960XGL, NP960XGL-XG1BR, NP960XGLZ-EXP...)               |
| **Display**        | 16" WQXGA+ (2880 x 1800) AMOLED Touchscreen                                           |
| **Processor**      | Intel Core Ultra 9 185H (Meteor Lake, 16C/22T, up to 5.1 GHz, 24 MB cache)            |
| **GPU**            | NVIDIA GeForce RTX 4070 Laptop 8 GB GDDR6                                             |
| **RAM**            | 32 GB LPDDR5X (soldered, not expandable)                                              |
| **Storage**        | 1 TB NVMe PCIe Gen4 SSD (expandable via secondary M.2 slot, depending on model)       |
| **Battery**        | 76 Wh (built-in, lithium-ion)                                                         |
| **Charger**        | 140 W USB-C (20V, 5A or 28V, 5A)                                                      |
| **Ports**          | 2x Thunderbolt 4 (USB-C), 1x HDMI 2.1, 1x USB-A 3.2, 1x 3.5 mm audio jack, 1x MicroSD |
| **Wireless**       | Wi-Fi 6E (802.11ax), Bluetooth 5.3                                                    |
| **Webcam**         | 1080p FHD, OmniVision OV02C10 sensor, IPU6 (MIPI)                                     |
| **Audio**          | 2x stereo speakers (AKG), dual microphone, Realtek ALC298 codec                       |
| **Keyboard**       | Backlit keyboard, ABNT2 (BR) or US layout, integrated fingerprint sensor              |
| **Touchpad**       | Large, Windows Precision gesture-compatible                                           |
| **Fingerprint**    | Integrated into the power button, Windows Hello-compatible                            |
| **Dimensions**     | 355.4 x 250.4 x 16.5 mm                                                               |
| **Other**          | Ambient light sensor, Samsung Multi Control support, Second Screen, Quick Share       |
| **Certifications** | ENERGY STAR, RoHS, FCC, CE                                                            |

* [User Manual](https://downloadcenter.samsung.com/content/UM/202506/20250618100447559/94xXGK_96xXGK_96xXGL_Win11_UM_ENG_Rev.2.0_250602.pdf)
* [Specifications](https://www.samsung.com/br/support/model/NP960XGL-XG1BR/)

> [!TIP]
> **Recommended kernel:** Use kernel **6.15 or newer** to ensure proper recognition of Galaxy Book4 Ultra hardware. Earlier versions have significant compatibility limitations.
>
> If you are not an advanced user, I recommend using Windows 11, which ships preinstalled and works perfectly on this model.

---

## Contents

- [Fedora on the Samsung Galaxy Book4 Ultra](#fedora-on-the-samsung-galaxy-book4-ultra)
  - [Quick Install](#quick-install)
  - [Contents](#contents)
  - [Dedicated Repositories](#dedicated-repositories)
    - [Summary of the current camera solution](#summary-of-the-current-camera-solution)
    - [Why there is a dedicated camera app](#why-there-is-a-dedicated-camera-app)
    - [Current interfaces](#current-interfaces)
      - [Galaxy Book Câmera](#galaxy-book-camera)
      - [Galaxy Book Setup](#galaxy-book-setup)
  - [Current Status](#current-status)
  - [Internal Speakers (Realtek ALC298)](#internal-speakers-realtek-alc298)
    - [Recommended flow](#recommended-flow)
    - [Verification](#verification)
  - [Internal Camera (IPU6/OV02C10)](#internal-camera-ipu6ov02c10)
    - [Current solution](#current-solution)
    - [What no longer makes sense to use](#what-no-longer-makes-sense-to-use)
    - [Verification](#verification-1)
  - [Fingerprint Reader](#fingerprint-reader)
    - [Recommended flow](#recommended-flow-1)
    - [Verification](#verification-2)
  - [Dedicated Graphics Chip (NVIDIA RTX 4070)](#dedicated-graphics-chip-nvidia-rtx-4070)
    - [Solution: NVIDIA driver installation](#solution-nvidia-driver-installation)
    - [Verification](#verification-3)
  - [Quick Diagnostics](#quick-diagnostics)
  - [Other Reports](#other-reports)

---

## Dedicated Repositories

The work for this notebook was split into dedicated repositories for webcam, audio, and installation:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  Tuned `ov02c10` driver packaged as an `akmod` for Fedora.
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  Camera app with a native GNOME UI using `GTK4` and `libadwaita`, built on `libcamera`.
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  Installation and diagnostics assistant with a graphical interface for camera, browser/messenger webcam support, NVIDIA, the platform `balanced` usage profile, and GNOME integrations.
- [`fedora-galaxy-book-max98390`](https://github.com/regiscaio/fedora-galaxy-book-max98390)
  `MAX98390` support packaged as an `akmod` for the internal speaker amplifiers.

These four projects consolidate the current line of webcam, audio, and diagnostics support on the Galaxy Book4 Ultra and reduce the need to repeat scattered scripts and workarounds inside this main guide.

### Summary of the current camera solution

The flow that makes the most sense today for anyone who wants to use the webcam on Fedora is:

1. install the `ov02c10` driver packaged in `fedora-galaxy-book-ov02c10`;
2. install the `Galaxy Book Câmera` app, with a native GNOME UI;
3. use `Galaxy Book Setup` for diagnostics, Intel IPU6 stack repair, and exposing the camera to browsers and messaging apps.

In other words: this main repository remains the **general notebook guide**, while active webcam maintenance has moved into the three dedicated projects above.

### Why there is a dedicated camera app

The native GNOME camera app was an important reference for design and desktop integration, but the Galaxy Book4 Ultra case required a more controlled path.

In practice, the problem was not only “opening the camera”:

- the `OV02C10` sensor required a tuned driver;
- the Intel IPU6 stack had to be treated as part of the solution;
- the direct `libcamera` path needed its own tuning;
- exposing the webcam to browsers and messaging apps required a separate V4L2 bridge in some scenarios.

For that reason, the solution was split into three fronts:

- one repository for the driver;
- a camera app with a native GNOME UI for the main preview, photo, and video flow;
- a graphical assistant for installation, repair, and compatibility with the rest of the system.

In other words: the goal was not to abandon Fedora's native app out of preference, but to create a dedicated solution for hardware that, in this case, needed more than the generic desktop flow.

Today, the native Fedora/GNOME camera app can already work on this notebook, which is a clear sign that the overall system stack has become healthier. That does not mean, however, that it delivers the same experience as `Galaxy Book Câmera`.

In practice, the current visual difference is this:

- the native Fedora app tends to show a more processed image, with color and white balance that look more pleasant out of the box;
- `Galaxy Book Câmera` uses a direct `libcamera` path, visually more raw and closer to the sensor, preserving more fine detail and offering much more control over the image.

In other words: both paths can work today, but they serve different priorities. The native Fedora app is already a good sign of system compatibility, while `Galaxy Book Câmera` remains the best option when the priority is detail, sensor tuning, and adjustment flexibility.

### Current interfaces

#### Galaxy Book Câmera

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-camera-icon.svg" alt="Galaxy Book Câmera icon" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Câmera</strong><br>
      Camera app with a native GNOME UI for the main preview, photo, and video flow on the Galaxy Book4 Ultra.<br>
      Repository: <a href="https://github.com/regiscaio/fedora-galaxy-book-camera">github.com/regiscaio/fedora-galaxy-book-camera</a>
    </td>
  </tr>
</table>

Main screen:

![Galaxy Book Câmera — main screen](img/app-camera-galaxy.png)

`About` modal:

![Galaxy Book Câmera — About](img/app-camera-galaxy-about.png)

#### Galaxy Book Setup

<table>
  <tr>
    <td width="132" align="center" valign="middle">
      <img src="img/galaxybook-setup-icon.svg" alt="Galaxy Book Setup icon" width="96">
    </td>
    <td valign="middle">
      <strong>Galaxy Book Setup</strong><br>
      Graphical installation, repair, and diagnostics assistant for camera, browser/messaging apps, NVIDIA, and desktop integrations.<br>
      Repository: <a href="https://github.com/regiscaio/fedora-galaxy-book-setup">github.com/regiscaio/fedora-galaxy-book-setup</a>
    </td>
  </tr>
</table>

Home screen:

![Galaxy Book Setup — home screen](img/app-setup-galaxy.png)

Diagnostics screen:

![Galaxy Book Setup — diagnostics](img/app-setup-galaxy-diagnostic.png)

`About` modal:

![Galaxy Book Setup — About](img/app-setup-galaxy-about.png)

---

## Current Status


| Component           | Revalidated status in April 2026 | Current practical reading                                                                                                                                                              |
| :------------------ | :------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Internal audio**  |              Works               | The current internal speaker path already works with the dedicated `MAX98390` line, packaged in `fedora-galaxy-book-max98390` and integrated into `fedora-galaxy-book-setup`.          |
| **Internal camera** |              Works               | The current stack already allows using the camera both in the native Fedora app and in the dedicated solution, with packaged `ov02c10`, `Galaxy Book Câmera`, and `Galaxy Book Setup`. |
| **Fingerprint**     |         Partial/unstable         | `fprintd` detects the Egis sensor, but persistent enrollment and post-suspend behavior still need continuous validation.                                                               |
| **NVIDIA RTX 4070** |        Works with caveats        | Proprietary modules load, including with Secure Boot enabled, but kernel updates and verification tools still require attention.                                                       |
| **BIOS/Firmware**   |           Recent risks           | Recent updates are still associated with throttling, dock failures, and Bluetooth regressions.                                                                                         |

---

## Internal Speakers (Realtek ALC298)

Today, the **internal speakers already work** on the Galaxy Book4 Ultra through the dedicated path that combines the **Realtek ALC298** codec with `MAX98390` amplifier support.

In practice, internal audio no longer depends on a set of loose quirks inside this README and is now maintained in dedicated repositories:

- [`fedora-galaxy-book-max98390`](https://github.com/regiscaio/fedora-galaxy-book-max98390)
  concentrates the packaged `RPM/akmod` support for the `MAX98390` path;
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  organizes diagnostics and quick actions for the internal speaker path.

In other words: internal audio has already moved out of the “local investigation” state and into an installable, reproducible flow on Fedora.

![alt text](img/settings-audio.png)

### Recommended flow

Today, the path that makes sense for audio is:

```bash
sudo dnf config-manager addrepo --from-repofile=https://packages.caioregis.com/fedora/caioregis.repo
sudo dnf install galaxybook-setup akmod-galaxybook-max98390
```

After that:

1. open `Galaxy Book Setup`;
2. review the `Internal speakers` diagnostic;
3. run the `Enable internal speakers` action;
4. validate the `Speaker` output in the system.

### Verification

```bash
modinfo -n snd-hda-scodec-max98390
modinfo -n snd-hda-scodec-max98390-i2c
wpctl status
lsmod | grep snd_hda_scodec_max98390
speaker-test -c 2 -t wav
```

> [!IMPORTANT]
> The report on [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220363)
> remains useful as technical history for the original problem. It describes a
> state where the kernel detected the hardware, but the routing for the internal
> speakers was still not closed in a usable way in the default stack.
>
> In the bug itself, there is also a reply from **Zhang Heng on December 23, 2025** saying that the speaker mapping at `0x17` looks normal and suggesting testing three models in the kernel:
>
> - `alc298-samsung-amp-v2-2-amps`
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
>
> He also suggests comparing the codec dump with Windows if none of them work.
> In April 2026, that context **is still useful as upstream explanation**, but
> it no longer describes the current state of the notebook with the dedicated
> `MAX98390` line. Before that, my local setup ended up depending on these two
> parameters in `snd-hda-intel`:
>
> - `model=alc298-samsung-amp-v2-2-amps`
> - `patch=alc298-internal-amp.fw`
>
> This matters because, on **April 5, 2026**, I was already using
> `alc298-samsung-amp-v2-2-amps`, that is, one of the alternatives suggested in
> the bug itself.
>
> In other words: the problem still cannot be treated as “solved in upstream
> pure form”, but it is also no longer correct to describe the notebook as if the
> internal audio simply did not work.

> [!CAUTION]
> Methods that I **no longer** consider part of the main flow:
>
> - `speaker-init.service`
> - `samsung-audio-fix.service`
> - startup scripts using `hda-verb`
> - concentrating audio troubleshooting only on local `snd-hda-intel` quirks inside this README
>
> These paths are now legacy in my setup. In April 2026, the old audio services were failing at boot and no longer defined the real behavior of the system.
>
> The `ALC298` context is still important for upstream and for comparing behavior with Windows, but the practical end-user path should now go through the dedicated `MAX98390` repository and `Galaxy Book Setup`.

> [!NOTE]
> If you need to test the more historical side of the codec, the models below
> remain the most coherent variants for comparison:
>
> - `alc298-samsung-amp-v2-4-amps`
> - `alc298-samsung-amp`
> - compare the codec dump with Windows

---

## Internal Camera (IPU6/OV02C10)

The internal camera of the Galaxy Book4 Ultra **now works through a dedicated solution**, made up of a tuned `ov02c10` driver, a camera app with a native GNOME UI, and a graphical installation and diagnostics assistant.

> [!IMPORTANT]
> The upstream report on [bugzilla.kernel.org](https://bugzilla.kernel.org/show_bug.cgi?id=220364) remains relevant to explain the original OmniVision OV02C10 sensor failure. The underlying problem is still the external `26 MHz` clock, which is incompatible with the in-tree driver path that expected `19.2 MHz`.
>
> When the system falls back to the in-tree driver, the error is still this:
>
> ```text
> external clock 26000000 is not supported
> probe with driver ov02c10 failed with error -22
> ```
>
> The difference is that there is now a functional path maintained outside this main guide.

![Galaxy Book Câmera — main flow](img/app-camera-galaxy.png)

### Current solution

The repositories that concentrate this solution are:

- [`fedora-galaxy-book-ov02c10`](https://github.com/regiscaio/fedora-galaxy-book-ov02c10)
  keeps the `ov02c10` module aligned with Fedora's Intel IPU6 stack, the `akmod` packaging, and automatic driver loading at boot;
- [`fedora-galaxy-book-camera`](https://github.com/regiscaio/fedora-galaxy-book-camera)
  delivers the camera app with a native GNOME UI for daily use;
- [`fedora-galaxy-book-setup`](https://github.com/regiscaio/fedora-galaxy-book-setup)
  organizes diagnostics and quick actions for installation, repair, driver priority tuning, Intel IPU6 stack restoration, and enabling the V4L2 webcam for browsers and messaging apps, while also tracking NVIDIA, the `balanced` profile, and desktop integrations.

The work in these repositories builds on lessons from the community fix:

- <https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix/>

Today, if I need the webcam on Fedora, the recommended path is no longer to keep insisting on old manual workarounds inside this repository, but to use the dedicated driver and the native GNOME UI app maintained in the repositories above.

> [!NOTE]
> When updating local RPMs for the driver outside a repository, include
> `galaxybook-ov02c10-kmod-common`, `akmod-galaxybook-ov02c10`, and
> `kmod-galaxybook-ov02c10` together. An old `kmod-galaxybook-ov02c10`
> metapackage can pin the previous `akmod` version and make `dnf` ignore the
> updated fixed module.

In practice, `Galaxy Book Setup` became the central point for:

- installing the camera stack;
- rebuilding the driver with `akmods`;
- adjusting the priority of the fixed module when the system falls back to the in-tree `ov02c10`;
- validating the camera in `libcamera`;
- exposing the internal camera as a V4L2 webcam for Meet, Teams, Discord, and other WebRTC apps;
- also tracking NVIDIA, the platform `balanced` profile, and useful GNOME extensions.

It is worth making one point explicit that has already changed since the first iterations of this investigation: the **native Fedora/GNOME camera app can already work** on this notebook in the current stack state.

That said, this does not make `Galaxy Book Câmera` irrelevant. The two paths deliver different experiences:

- the native Fedora app tends to show a more processed image, with color and white balance that look more pleasant by default;
- `Galaxy Book Câmera` uses a direct `libcamera` path, visually more raw and closer to the sensor, preserving more fine detail and offering much more flexible image control.

In other words: the camera stack already allows better desktop compatibility, but the dedicated app remains the best option when the priority is detail, sensor tuning, and image control.

### What no longer makes sense to use

The following paths are now legacy and **do not** represent the current base camera solution:

- `modprobe ov02c10 clock_frequency=19200000`
- `ov02c10-clock-fix.service`
- using `v4l2-relayd` + `icamerasrc` as a way to “prove” the sensor's primary detection

The old experiment with `clock_frequency=19200000` is obsolete. The current module responds with:

```text
ov02c10: unknown parameter 'clock_frequency' ignored
```

### Verification

```bash
modinfo -n ov02c10
journalctl -b -k | grep -i ov02c10
cam -l
journalctl -b -u akmods --no-pager
```

> [!CAUTION]
> **Common error when the system fell back to the in-tree driver**:
> ```external clock 26000000 is not supported ``` \
> ```probe with driver ov02c10 failed with error -22```
>
> This indicates that the fixed module was not loaded at boot and that the system fell back to the in-tree copy from the kernel.
>
> In that situation, the recommended path is:
>
> - open `Galaxy Book Setup`;
> - review the `akmods` status and the active module;
> - or consult the README for `fedora-galaxy-book-ov02c10` for the repair flow.

> [!NOTE]
> The bridge with `v4l2-relayd`, `icamerasrc`, and `v4l2loopback` remains
> valid, but it now serves a different purpose: **compatibility with browsers
> and messaging apps**. It does not replace `libcamera` validation; it comes
> later, to expose the camera as a V4L2 webcam for WebRTC apps.

---

## Fingerprint Reader

The fingerprint reader on this notebook is an **Egis / LighTuning ETU905A80-E** (`1c7a:05a1`).

On **April 20, 2026**, the local situation looked like this:

- the sensor appeared in `lsusb` as `1c7a:05a1 LighTuning Technology Inc. ETU905A80-E`;
- `fprintd` started normally;
- the `authselect` profile already had `with-fingerprint` enabled;
- the installed `libfprint` was `1.94.10`, which already includes the `egismoc` driver used by this sensor.

In other words: at this point, the reader is **not** in a hardware-without-support scenario. What still needed validation was day-to-day use with fingerprints actually enrolled, plus behavior after suspend.

![alt text](img/settings-fingerprint.png)

### Recommended flow

If the sensor appears, but enrollment or authentication fails, the safest path is still to reinstall the stack and enroll again:

```bash
sudo dnf reinstall fprintd libfprint
sudo systemctl restart fprintd
fprintd-delete $USER
fprintd-enroll
sudo authselect enable-feature with-fingerprint
sudo authselect apply-changes
```

### Verification

```bash
lsusb | grep -i '1c7a:05a1'
fprintd-list $USER
authselect current
fprintd-verify
```

> [!CAUTION]
> **Common error**:
> ```Failed to enroll fingerprint: Device or resource busy```
>
> For some reason, the sensor may be busy or not correctly configured at boot. As a consequence, it cannot locate the previously enrolled fingerprint and use it for authentication.

---

## Dedicated Graphics Chip (NVIDIA RTX 4070)

It works well with RPM Fusion, but it requires attention to kernel updates and Secure Boot with the MOK process.

In April 2026, the `nvidia`, `nvidia_modeset`, `nvidia_drm`, and `nvidia_uvm` modules were loaded with **Secure Boot enabled**. In other words: the driver itself was functional.

It is also worth separating two things that are often treated as if they were the same:

- the loaded NVIDIA driver;
- the `nvidia-smi` utility being installed.

In my case, the driver was working even without the `nvidia-smi` binary in `PATH`. On Fedora/RPM Fusion, that binary comes from the `xorg-x11-drv-nvidia-cuda` package, so it should be treated as an **optional administration/diagnostics tool**, not as a prerequisite for the driver to exist.

![alt text](img/settings-gpu.png)

### Solution: NVIDIA driver installation

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf update
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
sudo reboot
```

If the goal is **only** to have the driver working, the key package is `akmod-nvidia`.

If the goal is also to have `nvidia-smi`, then it makes sense to keep `xorg-x11-drv-nvidia-cuda` installed explicitly.

If it fails after an update:

```bash
sudo akmods --force
sudo dracut --force
sudo reboot
```

Disable Secure Boot if necessary.

### Verification

To verify the **driver**:

```bash
lsmod | grep nvidia
mokutil --sb-state
```

To verify the **`nvidia-smi` utility**:

```bash
nvidia-smi
```

> [!CAUTION]
> `nvidia-smi` should not be the only criterion for evaluating whether the driver is working.
>
> If the command does not even exist, that normally indicates the absence of the `xorg-x11-drv-nvidia-cuda` package, not the absence of the driver.
>
> **Common error**:
> ```Unable to determine the device handle for GPU0: 0000:01:00.0: Unknown Error``` \
> ```No devices were found```
>
> The NVIDIA driver is not loading correctly or Secure Boot is blocking the module. This happens after every kernel update, so you may need to reinstall the driver and configure it again. Although convenient, if you choose to disable Secure Boot you lose the extra security it provides, so do it at your own risk.

---

## Quick Diagnostics

| Component       | Diagnostic Command                                                                                                             |
| :-------------- | :----------------------------------------------------------------------------------------------------------------------------- |
| **Kernel**      | `uname -r`                                                                                                                     |
| **Audio**       | `aplay -l && wpctl status && cat /sys/module/snd_hda_intel/parameters/model && cat /sys/module/snd_hda_intel/parameters/patch` |
| **Camera**      | `modinfo -n ov02c10 && journalctl -b -k \| grep -i ov02c10 && cam -l && journalctl -b -u akmods --no-pager`                    |
| **Fingerprint** | `fprintd-list $USER && systemctl status fprintd.service --no-pager && journalctl -b \| grep -i fprint`                         |
| **NVIDIA**      | `lsmod \| grep nvidia && mokutil --sb-state && rpm -qa \| grep -i nvidia`                                                      |

## Other Reports

> [!NOTE]
> These links contain reports and experiences from other users with the Galaxy Book4 Ultra on Linux, covering everything from hardware problems to attempts at creative fixes.

- [Reverse-engineering attempt report for internal speakers on EndeavourOS](https://github.com/dgunay/galaxy-book4-pro-reverse-engineering)
- [Report using Fedora 42 (KDE) with Intel Xe Iris performance measurements](https://github.com/jusqua/galaxy-book4-linux)
- [Community fix for the Galaxy Book3 OV02C10 camera driver](https://github.com/abdallah-alkanani/galaxybook3-ov02c10-fix)

## License

This repository is distributed under **GPL-3.0-only**. See [LICENSE](LICENSE).
