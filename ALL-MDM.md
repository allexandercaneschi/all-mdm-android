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

1. Crie a tag `v<versão-headwind>-all.<n>`, por exemplo `v6.40-all.1`, e envie para o GitHub.
2. O GitHub Actions publica `all-mdm-agent-6.40-all.1.apk` no Releases.
3. No painel: **Aplicativos → ALL MDM → Adicionar versão**, envie o APK e escolha a versão
   nova nas configurações. Os celulares atualizam sozinhos.

Para compilar na sua máquina: `./gradlew assembleAlltecRelease` (com as variáveis acima exportadas).

## Atualizar com a Headwind

1. No GitHub, use **Sync fork** na branch `master`.
2. Faça merge de `master` em `all-branding` (conflitos só aparecem se a Headwind mexer no bloco `productFlavors`).
3. Gere uma tag nova.

O `versionCode` do agente é o da Headwind × 100 + o número da revisão ALL (o `n` da tag).
Assim, tanto uma versão nova da Headwind quanto uma correção só da ALL (`-all.2`, `-all.3`…)
sempre ficam maiores que a anterior e chegam aos celulares como atualização.
