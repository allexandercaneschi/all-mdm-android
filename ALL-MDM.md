# ALL MDM — agente Android

Fork do [Headwind MDM](https://github.com/h-mdm/hmdm-android) (Apache 2.0) com a marca da ALL Tecnologias.
Nenhum código Java foi alterado: tudo está no flavor `alltec`.

## O que muda no flavor `alltec`

| Item | Valor |
|---|---|
| Pacote | `br.com.alltecnologias.mdm` |
| Nome no celular | ALL MDM |
| Ícone e banner | Chip da ALL (`app/src/alltec/res`) |
| Cores | Azul #35A8E0 e fundo #0B1220 |
| Servidor padrão | `https://mdm.alltecnologias.com.br` (não pergunta a URL no primeiro uso) |
| Segredos | Lidos das variáveis `ALL_MDM_SHARED_SECRET` e `ALL_MDM_LIBRARY_API_KEY` |
| Assinatura | Chave da ALL, lida das variáveis `ALL_MDM_KEYSTORE*` |

O flavor `opensource` (original da Headwind) continua existindo e não foi alterado.

## Chave de assinatura (fazer uma única vez)

> **A chave não pode ser perdida.** Celulares provisionados como Device Owner só aceitam
> atualizações do agente assinadas com a mesma chave. Sem ela, cada aparelho precisaria de
> um novo reset de fábrica. Guarde a keystore e as senhas em pelo menos dois lugares
> (ex.: cofre de senhas e backup offline). Nunca coloque a keystore no repositório.

```bash
keytool -genkeypair -v -keystore all-mdm.jks -alias all-mdm \
  -keyalg RSA -keysize 4096 -validity 36500 \
  -dname "CN=ALL Tecnologias, O=ALL Tecnologias, C=BR"

base64 -w0 all-mdm.jks > all-mdm.jks.b64
```

Cadastre em **Settings → Secrets and variables → Actions** do repositório:

| Secret | Conteúdo |
|---|---|
| `ALL_MDM_KEYSTORE_BASE64` | conteúdo de `all-mdm.jks.b64` |
| `ALL_MDM_KEYSTORE_PASSWORD` | senha da keystore |
| `ALL_MDM_KEY_ALIAS` | `all-mdm` |
| `ALL_MDM_KEY_PASSWORD` | senha da chave |
| `ALL_MDM_SHARED_SECRET` | o mesmo `SHARED_SECRET` do `.env` do servidor |
| `ALL_MDM_LIBRARY_API_KEY` | um valor longo e aleatório |

## Gerar uma versão

1. Altere o arquivo `ALL_VERSION` (formato `X.Y.Z`, ex.: `1.0.1`) e faça push na branch `all/main`.
2. O GitHub Actions compila, assina, cria a tag `v1.0.1` e publica `all-mdm-agent-1.0.1.apk` no Releases.
   Pushes que não mudam o `ALL_VERSION` só compilam (o APK fica como artefato do workflow, para teste).
3. No painel: **Aplicativos → ALL MDM → Adicionar versão**, envie o APK e escolha a versão
   nova nas configurações. Os celulares atualizam sozinhos.

Para compilar na sua máquina: `./gradlew assembleAlltecRelease` (com as variáveis acima exportadas).

## Atualizar com a Headwind

1. No GitHub, use **Sync fork** na branch `master`.
2. Faça merge de `master` em `all/main` (conflitos só aparecem se a Headwind mexer no bloco `productFlavors`).
3. Suba o número em `ALL_VERSION` e faça push.

A versão do agente segue a numeração da ALL (`ALL_VERSION`), não a da Headwind.
O `versionCode` é `X*1000000 + Y*1000 + Z` (1.0.0 → 1000000), então toda versão nova da ALL
fica maior que a anterior e chega aos celulares como atualização. A versão base da Headwind
aparece na descrição de cada release.
