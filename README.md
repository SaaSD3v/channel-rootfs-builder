# Channel RootFS Builder — Alpine

Rootfs **Alpine 3.24 / OpenRC** para o Motorola Moto G7 Play (`channel`). Este README descreve **somente a branch `alpine`**, não o kernel independente nem outras distribuições.

## Build e arquivos desta branch

- Kernel: `SaaSD3v/linux`, branch `msm8953/latest`, arquitetura `arm64`.
- Script: `alpine/build.sh`.
- Workflow: `.github/workflows/alpine.yml` (também acessível pelo lançador na `main`).
- Artefato de rootfs: `channel-alpine-rootfs`.
- Imagem: `alpine-channel-rootfs.ext4.zst` (ext4 raw comprimido).
- Label ext4: `alpine`.
- UUID ext4: `89530000-6320-4000-8000-000000000001`.
- O boot do Channel é direto, sem initramfs. Não confunda o UUID ext4 com o PARTUUID da partição Android `userdata`.

O workflow pode reutilizar um checkpoint do kernel ou compilar temporariamente um kernel para instalar módulos compatíveis. O rootfs não substitui os artefatos de boot.

---

## Preparar a imagem: ext4 raw ou Android sparse

Depois de extrair o ZIP do artefato do GitHub Actions, execute **no computador**:

~~~sh
zstd -d -k alpine-channel-rootfs.ext4.zst
file alpine-channel-rootfs.ext4
~~~

Se `file` identificar **ext4 raw**, pode transformar a imagem em **Android sparse** antes de gravar:

~~~sh
img2simg alpine-channel-rootfs.ext4 alpine-sparse.img
fastboot flash userdata alpine-sparse.img
~~~

Se `file` indicar que a imagem **já é Android sparse**, **não** rode `img2simg`: grave o arquivo existente com `fastboot flash userdata alpine-channel-rootfs.ext4`. Alguns fastboots também aceitam ext4 raw diretamente: `fastboot flash userdata alpine-channel-rootfs.ext4`.

Para converter sparse em raw quando necessário: `simg2img alpine-sparse.img alpine-extraido.ext4`. No computador Debian/Ubuntu, `img2simg` e `simg2img` costumam estar no pacote `android-sdk-libsparse-utils`.

**Gravar `userdata` destrói seu conteúdo anterior.** Verifique a partição e faça backup. Sparse não muda o tamanho do filesystem.

---

## Expandir o `/` ext4 para o tamanho da partição

Depois de iniciar o **telefone**, como root, descubra onde `/` está montado:

~~~sh
findmnt -n -o SOURCE,FSTYPE /
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINTS
df -h /
~~~

**Apenas para `/` montado em ext4 e depois de confirmar a partição real**, substitua o caminho abaixo pelo dispositivo correto:

~~~sh
resize2fs /dev/PARTICAO_ROOT_CONFIRMADA
df -h /
~~~

Sem tamanho explícito, `resize2fs` pode aumentar o ext4 até o tamanho da partição, se o kernel suportar expansão online. **Não execute `e2fsck` em `/` montado.** Se faltar a ferramenta ou a operação online não funcionar, use um ambiente de recuperação com o ext4 desmontado e backup. O comando não redimensiona a partição GPT.

