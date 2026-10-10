# Channel RootFS Builder — Moto G7 Play

Construtor separado de **kernel mainline e rootfs ARM64** para o Motorola Moto G7 Play (`channel`, Qualcomm SDM632). Este projeto usa o kernel de [SaaSD3v/linux](https://github.com/SaaSD3v/linux), branch `msm8953/latest`. Ele **não** é o construtor do Moto G5S Plus (Sanders) nem a matriz experimental de dez distribuições.

## Branches, workflows e artefatos

| Branch | Sistema/componente | Workflow na `main` | Artefato |
| --- | --- | --- | --- |
| `main` | Kernel + DTB + módulos + boot | `build-mainline.yml` | `channel-mainline-kernel-<sha>` |
| `debian` | Debian 13 (Trixie), systemd | `debian.yml` | `channel-debian-rootfs` |
| `ubuntu` | Ubuntu 26.04.1, systemd | `ubuntu.yml` | `channel-ubuntu-rootfs` |
| `alpine` | Alpine 3.24, OpenRC | `alpine.yml` | `channel-alpine-rootfs` |

Os workflows das distribuições aparecem na `main` para disponibilizar o botão **Run workflow**, mas fazem checkout da branch correspondente. Nas respectivas branches ficam os arquivos de criação do rootfs.

No GitHub: **Actions → Build mainline kernel → Run workflow** primeiro, se quiser um checkpoint reutilizável. Depois execute **Build Debian/Ubuntu/Alpine rootfs**. Os inputs `reuse_kernel` e `kernel_run_id` só selecionam reutilização de um kernel já compilado. Sem checkpoint válido, o workflow pode compilar o kernel temporariamente e usar seus módulos, sem publicá-lo como artefato principal.

Imagens dos rootfs e arquivos associados:

- `debian-channel-rootfs.ext4.zst`, `ubuntu-channel-rootfs.ext4.zst` ou `alpine-channel-rootfs.ext4.zst`;
- `build-info.txt` e somas SHA-256;
- imagens kernel/boot vêm do workflow **Build mainline kernel**, não dos três artefatos de rootfs.

Para extrair no computador, por exemplo:

```sh
zstd -d -k debian-channel-rootfs.ext4.zst
```

## Boot e identificação correta da partição

O `boot-channel.img` usa kernel e DTB Channel **sem initramfs**. O boot monta diretamente `userdata` por:

```text
root=PARTUUID=76dbdefa-f243-cd22-5da5-9374e6ad318b rootfstype=ext4 rootwait rw
```

O UUID ext4 das imagens é `89530000-6320-4000-8000-000000000001` e os labels variam conforme a distribuição (`debian`, `ubuntu`, `alpine`). **UUID ext4 e PARTUUID GPT não são intercambiáveis.** Antes de qualquer flash, valide o particionamento, a imagem e os módulos de kernel; não confunda um build concluído com boot validado em aparelho.

## SSH pela USB

Os rootfs configuram uma forma fixa de desenvolvimento (`ssh_auth=ssh`) pela rede USB RNDIS. O telefone usa `172.16.42.1/24`, com endereços DHCP USB do intervalo `172.16.42.2–172.16.42.20`. Do computador conectado:

```sh
ssh root@172.16.42.1
```

Não são solicitados modos de senha ou chave de usuário no dispatch. Esse acesso concede root total ao host conectado; não use computadores USB não confiáveis. Apenas configurar `ListenAddress` não garante isolamento por interface, então confirme isso no hardware.

## Conectar o Channel à Internet (NetworkManager)

**Debian, Ubuntu e Alpine** deste projeto incluem NetworkManager e usam `wlan0` para Wi-Fi; a rede de serviço `usb0` deve permanecer gerenciada pelo gadget, não pelo NetworkManager.

No terminal do **celular**:

```sh
nmcli general status
nmcli device status
nmcli radio wifi on

# Procurar redes disponíveis
nmcli device wifi rescan ifname wlan0
nmcli -f IN-USE,SSID,SIGNAL,SECURITY device wifi list ifname wlan0

# Digitar a senha interativamente
nmcli --ask device wifi connect "NOME_DA_REDE" ifname wlan0

# Verificar o resultado
nmcli connection show --active
ip -4 address show wlan0
ip route
getent hosts debian.org
ping -c 3 1.1.1.1
```

Para reconectar: `nmcli connection up "NOME_DA_CONEXAO"`. Se preferir informar a senha no comando, `nmcli device wifi connect "SSID" password "SENHA" ifname wlan0` funciona, mas pode expô-la no histórico. Se o dispositivo Wi-Fi estiver ausente, confira firmware WCNSS, driver `wcn36xx` e o serviço:

```sh
# Debian / Ubuntu
systemctl status NetworkManager --no-pager
journalctl -b -u NetworkManager -n 80 --no-pager

# Alpine / OpenRC
rc-service networkmanager status
```

## Data e hora: ajuste temporário (sem NTP configurado pelo builder)

Os scripts de rootfs deixaram de instalar/habilitar `chronyd` ou `systemd-timesyncd` explicitamente. `TZ` altera o fuso **mostrado**, mas não define a data real. Horário incorreto pode impedir HTTPS, `apt`/`apk` e conexões TLS.

Como root **dentro do celular**, substitua a data e a hora **UTC** ilustrativas abaixo pelo instante correto:

```sh
date -u
date -u -s "2026-10-10 12:00:00"   # EXEMPLO, não é a hora atual garantida
date -u
date
```

É um ajuste manual, não uma sincronização periódica; um RTC incorreto pode fazer a hora voltar atrás após reboot. `TZ=America/Porto_Velho date` só altera como a hora é exibida quando os dados de fuso existem.
