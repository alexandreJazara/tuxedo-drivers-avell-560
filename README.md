# Tuxedo Drivers - Suporte a Teclado RGB para Avell Storm 560

Modificação (patch) do driver oficial da TUXEDO Computers para viabilizar o controle de retroiluminação RGB e os atalhos físicos do teclado no notebook **Avell Storm 560** (chassi Tongfang/Uniwill) em distribuições Linux (Linux Mint, Ubuntu e derivados).

---

## ⚠️ AVISO LEGAL E ISENÇÃO DE RESPONSABILIDADE (DISCLAIMER)

> **LEIA COM ATENÇÃO:**
>
> 1. **SEM VÍNCULO OU SUPORTE OFICIAL:** Este repositório é uma iniciativa independente e de código aberto. Não possui qualquer vínculo comercial, chancela ou suporte por parte da **Avell High Performance** ou da **TUXEDO Computers GmbH**.
> 2. **ISENÇÃO TOTAL DE RESPONSABILIDADE:** Este código foi adaptado e testado exclusivamente no hardware de desenvolvimento do autor (**Avell Storm 560**, AMD Ryzen 7, NVIDIA RTX 5060, rodando Linux Mint). O autor **NÃO SE RESPONSABILIZA** por quaisquer danos diretos, indiretos, falhas no sistema operacional, instabilidades, congelamentos, queima de componentes ou perda de garantia decorrentes do uso, instalação ou modificação destes drivers em qualquer equipamento.
> 3. **USO POR SUA CONTA E RISCO:** Todo e qualquer procedimento aqui descrito deve ser realizado por usuários conscientes dos riscos técnicos de compilar e carregar módulos modificados diretamente no kernel do Linux.

---

## 📌 O que foi modificado?

- **Roteamento de GUID WMI:** Redirecionamento das chamadas da controladora WMI para o identificador `UNIWILL_WMI_MGMT_GUID_BA` (`ABBC0F6D-8EA1-11D1-00A0-C90629100000`), utilizado pela BIOS da Avell.
- **Tratamento de Retorno ACPI:** Suporte a retornos do tipo `ACPI_TYPE_INTEGER` nas avaliações do Embedded Controller (EC).
- **Liberação de Interface de LEDs:** Bypass de checagens exclusivas de BIOS Tuxedo e ativação de perfil de iluminação RGB de 1 zona (`rgb:kbd_backlight`).
- **Build seguro:** Correção no Makefile (`CURDIR`) para evitar erros de compilação ao rodar sob privilégios de `sudo`.

---

## 🚀 Como Compilar e Instalar

### Pré-requisitos
```bash
sudo apt update
sudo apt install build-essential git dkms linux-headers-$(uname -r)
```

Instalação
Bash
# Compilar os módulos
make clean && make

# Instalar no sistema
sudo make install
sudo depmod -a

# Carregar os drivers no kernel
sudo modprobe tuxedo_keyboard
sudo modprobe uniwill_wmi
⌨️ Atalhos de Hardware Ativos (Avell Storm 560)
Com o driver carregado, o teclado passa a responder pelos seguintes atalhos físicos no teclado numérico:

Fn + /: Alternar entre as cores RGB (Vermelho, Verde, Azul, Amarelo, Magenta, Ciano, Branco).

Fn + *: Ligar / Desligar a iluminação.

Fn + + / Fn + -: Aumentar / Diminuir o brilho do teclado.

Controle manual via sysfs:

Bash
# Brilho máximo:
echo 255 | sudo tee /sys/class/leds/rgb:kbd_backlight/brightness

# Definir cor branca (R G B):
echo "255 255 255" | sudo tee /sys/class/leds/rgb:kbd_backlight/multi_intensity


## README ORIGIN


# Table of Content
- <a href="#description">Description</a>
- <a href="#building-and-install">Building and Install</a>
- <a href="#troubleshooting">Troubleshooting</a>
- <a href="#regarding-upstreaming-of-tuxedo-drivers">Regarding upstreaming of tuxedo-drivers</a>

# Description
Drivers for several platform devices for TUXEDO notebooks meant for DKMS.

## Features implemented by this driver package
- Fn-keys
- Keyboard backlight
- Fan control
- Power control
- Other sensors
- Hardware specific userspace quirks

## Modules included in this package
- clevo_acpi
- clevo_wmi
- tuxedo_keyboard
- uniwill_wmi
- ite_8291
- ite_8291_lb
- ite_8297
- ite_829x
- tuxedo_io
- tuxedo_compatibility_check
- tuxedo_nb05_keyboard
- tuxedo_nb05_power_profiles
- tuxedo_nb05_ec
- tuxedo_nb05_sensors
- tuxedo_nb04_keyboard
- tuxedo_nb04_wmi_ab
- tuxedo_nb04_wmi_bs
- tuxedo_nb04_sensors
- tuxedo_nb04_power_profiles
- tuxedo_nb04_kbd_backlight
- tuxedo_nb05_kbd_backlight
- tuxedo_nb02_nvidia_power_ctrl
- tuxedo_nb05_fan_control
- tuxi_acpi
- tuxedo_tuxi_fan_control
- stk8321
- gxtp7380

# Building and Install

## Dependencies:
All:
- make

`make package-*`:
- [simple-package-creator](https://gitlab.com/tuxedocomputers/development/packages/simple-package-creator)
- [simple-package-tools](https://gitlab.com/tuxedocomputers/development/packages/simple-package-tools)

# Troubleshooting

## The keyboard backlight control and/or touchpad toggle key combinations do not work
For all devices with a touchpad toggle key(-combo) and some devices with keyboard backlight control key-combos the driver does nothing more then to send the corresponding key event to userspace where it is the desktop environments duty to carry out the action. Some smaller desktop environments however don't bind an action to these keys by default so it seems that these keys don't work.

Please refer to your desktop environments documentation on how to set custom keybindings to fix this.

For keyboard brightness control you should use the D-Bus interface of UPower as actions for the key presses.

For touchpad toggle on X11 you can use `xinput` to enable/disable the touchpad, on Wayland the correct way is desktop environment specific.

# Regarding upstreaming of tuxedo-drivers
The code, while perfectly functional, is currently not in an upstreamable state. That being said we started an upstreaming effort and the first small part, the keyboard backlight control for the Sirius 16 Gen 1 & 2, already got accepted.

If you want to hack away at this matter yourself please follow the following precautions and guidelines to avoid breakages on both software and hardware level:
- Involve us in the whole process. Nothing is won if at some point tuxedo-control-center or the dkms variant of tuxedo-drivers stops working. Especially when you send something to the LKML, please set us in the cc.
- We mostly can't share documentation, but we can answer questions.
- Code interacting with the EC, which is most of tuxedo-drivers, can brick devices and therefore must be ensured to only run on compatible and tested devices.
- If you use tuxedo-drivers as a reference or code snippets from it, a "Codeveloped-by:\<name\> \<tuxedo_email\>" must be included in your upstream commit, with \<name\> and \<tuxedo_email\> depending on the actual part of tuxedo-drivers being used. Please talk to us regarding this.
