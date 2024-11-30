+++
title = "Сборка Rust для Windows 10 через GitLab Runner"
description = "Как настроить сборку Rust-проекта под Windows."
date = 2024-11-30
updated = 2024-11-30

[taxonomies]
tags = ["rust", "devops", "gitlab"]
categories = ["devops", "rust", "gitlab"]
+++

На днях собирал новый GitLab Runner на базе Windows 10 и наловил проблем. Событие редкое, многое забывается, поэтому пусть будет.
Далее - описан опыт как починить те проблемы с которыми столкнулся.

У проекта который собирал были вот такой `Cargo.toml`:

```toml
[package]
name = "redacted"
version = "1.3.1"
edition = "2021"

[profile.release]
opt-level = 'z'
lto = true
codegen-units = 1
panic = 'abort'

[dependencies]
anyhow = "1"

clap = "2.33.4"

reqwest = { version = "0.11.12", default-features = false, features = ["blocking", "json", "rustls-tls"] }

base64 = "0.12"

regex = "1"

serde = { version = "1.0", features = ["derive"] }
serde_derive = "1.0"
serde_json = "1.0"

chrono = "0.4"

log = "0.4"
log4rs = "1.2.0"
```

В качестве ОС - Windows 10 22H2 с обновлениями на ноябрь 2024.

Релизную сборку запускаем по классике:

```shell
cargo build --release
```

Дальше начинаются проблемы.

### Проблема №1: GitLab Runner не находит команду pwsh

Ошибка:

```shell
ERROR: Job failed (system failure): prepare environment: failed to start process: exec: “pwsh”: executable file not found in %PATH%.
```

Правим `c:\gitlab-runner\config.toml`, меняем `pwsh` на `powershell`.

Рестартуем службу `gitlab-runner`.


### Проблема №2: Установка Rust по умолчанию содержит пробелы в пути

Многие инсталляторы любят ставить приложения в `c:\Program Files\blablabla` и в большинстве случаев это работает.
В случае с софтом для разработки часто это приводит к неявным проблемам. Основная проблема - пробелы в пути.
Олдскульный софт будет писать ошибки, ругаться про пути и не находить то что там формально присутствует.

Я ставлю в `c:\rust`.

### Проблема №3: Скрытые зависимости Gitlab Runner

Ставим [git для Windows](https://git-scm.com/downloads/win).

### Проблема №4: Прописывать пути в системные переменные

Важно прописывать в системные переменные пути к софту который мы ставим:

![Системные переменные окружения](/images/rust/rust-gcc-windows.png "Системные переменные окружения")

После правок рестартуем службу `gitlab-runner`.

### Проблема №5: Хочу собирать проекты на другом диске

Допустим мы поставили `gitlab-runner` в `c:\gitlab-runner`, но на нём не так много места. Rust-проект может сожрать легко пару гигов, а если проектов много, то вообще беда.
Например, у нас есть диск `D:\` с достаточным объёмом.

В `c:\gitlab-runner\config.toml` следует указать путь для `builds_dir` (без пробелов):

```toml
concurrent = 1
check_interval = 0
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "my-sweet-windows-runner"
  url = "https://gitlab.com"
  id = 43796767
  token = "glrt-REDACTED"
  token_obtained_at = 2024-11-30T10:07:06Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "shell"
  shell = "powershell"
  builds_dir = "E:/builds" # <<<<< СЮДА
  [runners.custom_build_dir]
  [runners.cache]
    MaxUploadedArchiveSize = 0
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
```

И перезапустить службу `gitlab runner`.

### Проблема №6: gcc program not found

Ругается, что не найден сишный компилятор `gcc`:

```shell
error: failed to run custom build command for `ring v0.16.20`
Caused by:
  process didn't exit successfully: `E:\builds\t3_sbSPS6\0\projects\redacted\target\debug\build\ring-1c55f4d67b4150d6\build-script-build` (exit code: 101)
  --- stdout
  OPT_LEVEL = Some("0")
  TARGET = Some("x86_64-pc-windows-gnu")
  HOST = Some("x86_64-pc-windows-gnu")
  cargo:rerun-if-env-changed=CC_x86_64-pc-windows-gnu
  CC_x86_64-pc-windows-gnu = None
  cargo:rerun-if-env-changed=CC_x86_64_pc_windows_gnu
  CC_x86_64_pc_windows_gnu = None
  cargo:rerun-if-env-changed=HOST_CC
  HOST_CC = None
  cargo:rerun-if-env-changed=CC
  CC = None
  cargo:rerun-if-env-changed=CFLAGS_x86_64-pc-windows-gnu
  CFLAGS_x86_64-pc-windows-gnu = None
  cargo:rerun-if-env-changed=CFLAGS_x86_64_pc_windows_gnu
  CFLAGS_x86_64_pc_windows_gnu = None
  cargo:rerun-if-env-changed=HOST_CFLAGS
  HOST_CFLAGS = None
  cargo:rerun-if-env-changed=CFLAGS
  CFLAGS = None
  cargo:rerun-if-env-changed=CRATE_CC_NO_DEFAULTS
  CRATE_CC_NO_DEFAULTS = None
  DEBUG = Some("true")
  CARGO_CFG_TARGET_FEATURE = Some("cmpxchg16b,fxsr,sse,sse2,sse3")
  --- stderr
  running "gcc.exe" "-O0" "-ffunction-sections" "-fdata-sections" "-gdwarf-2" "-fno-omit-frame-pointer" "-m64" "-I" "include" "-Wall" "-Wextra" "-std=c1x" "-Wbad-function-cast" "-Wnested-externs" "-Wstrict-prototypes" "-pedantic" "-pedantic-errors" "-Wall" "-Wextra" "-Wcast-align" "-Wcast-qual" "-Wconversion" "-Wenum-compare" "-Wfloat-equal" "-Wformat=2" "-Winline" "-Winvalid-pch" "-Wmissing-field-initializers" "-Wmissing-include-dirs" "-Wredundant-decls" "-Wshadow" "-Wsign-compare" "-Wsign-conversion" "-Wundef" "-Wuninitialized" "-Wwrite-strings" "-fno-strict-aliasing" "-fvisibility=hidden" "-g3" "-DNDEBUG" "-c" "-oE:\\builds\\t3_sbSPS6\\0\\kamis-ops\\kam4z\\target\\debug\\build\\ring-80888969515fa4ed\\out\\aes_nohw.o" "crypto/fipsmodule/aes/aes_nohw.c"
  thread 'main' panicked at C:\Windows\system32\config\systemprofile\.cargo\registry\src\index.crates.io-6f17d22bba15001f\ring-0.16.20\build.rs:653:9:
  failed to execute ["gcc.exe" "-O0" "-ffunction-sections" "-fdata-sections" "-gdwarf-2" "-fno-omit-frame-pointer" "-m64" "-I" "include" "-Wall" "-Wextra" "-std=c1x" "-Wbad-function-cast" "-Wnested-externs" "-Wstrict-prototypes" "-pedantic" "-pedantic-errors" "-Wall" "-Wextra" "-Wcast-align" "-Wcast-qual" "-Wconversion" "-Wenum-compare" "-Wfloat-equal" "-Wformat=2" "-Winline" "-Winvalid-pch" "-Wmissing-field-initializers" "-Wmissing-include-dirs" "-Wredundant-decls" "-Wshadow" "-Wsign-compare" "-Wsign-conversion" "-Wundef" "-Wuninitialized" "-Wwrite-strings" "-fno-strict-aliasing" "-fvisibility=hidden" "-g3" "-DNDEBUG" "-c" "-oE:\\builds\\t3_sbSPS6\\0\\kamis-ops\\kam4z\\target\\debug\\build\\ring-80888969515fa4ed\\out\\aes_nohw.o" "crypto/fipsmodule/aes/aes_nohw.c"]: program not found
  note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

**Решение:**

1. Качаем и ставим [msys2](https://www.msys2.org/#installation).
2. Ставим по пути `c:\msys2` (это важно).
3. Добавляем в системные переменные окружения `c:\msys64\mingw64\bin`:
  ![gcc в системных переменных окружения](/images/rust/rust-gcc-windows.png "gcc в системных переменных окружения")
4. В ПУСК ищем `ming` и запускаем `MSYS2 MINGW64`, откроется терминал, где доставляем `gcc` командой: `pacman --sync mingw-w64-x86_64-gcc`
5. Рестартуем службу `gitlab-runner`.
