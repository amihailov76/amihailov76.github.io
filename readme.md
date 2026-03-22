# Создание личного сайта на Hugo с темой PaperMod

---

## Необходимые компоненты

| Компонент | Назначение | Версия в проекте |
|-----------|------------|-----------------|
| Hugo Extended | Генератор статического сайта | 0.158.0 |
| Git | Система контроля версий | 2.53.0 |
| Go | Нужен для Hugo Modules | 1.26.1 |
| Node.js | Опционально, для будущих расширений | 24.14.0 |

> PaperMod не требует PostCSS и npm для базовой работы.

---

## Этап 1 — Установка инструментов (Windows)

### Hugo Extended

1. Открыть: https://github.com/gohugoio/hugo/releases/latest
2. Скачать `hugo_extended_0.XXX.X_windows-amd64.zip` — важно слово `extended`
3. Распаковать, положить `hugo.exe` в `C:\Hugo\bin`
4. Добавить в PATH — в PowerShell от имени администратора:

```powershell
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Hugo\bin", "Machine")
```

5. Перезапустить PowerShell и проверить:

```powershell
hugo version
# hugo v0.158.0+extended windows/amd64
```

### Git

Скачать с https://git-scm.com/download/win, установить с настройками по умолчанию.

### Go

Скачать с https://go.dev/dl/ — файл `go1.XX.X.windows-amd64.msi`, установить с настройками по умолчанию.

### Разрешение скриптов PowerShell

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## Этап 2 — Создание проекта

### Шаг 1 — Новый сайт

```powershell
cd C:\Site
hugo new site my-site
cd my-site
git init
```

### Шаг 2 — Подключение PaperMod

```powershell
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

> Предупреждение про CRLF — нормально для Windows, игнорировать.

### Шаг 3 — Настройка hugo.yaml

Удалить `hugo.toml` и создать `hugo.yaml`:

```powershell
Remove-Item C:\Site\my-site\hugo.toml
```

Содержимое `hugo.yaml`:

```yaml
baseURL: http://localhost/
title: Александр Михайлов
theme: PaperMod

defaultContentLanguage: ru
defaultContentLanguageInSubdir: false

enableRobotsTXT: true
enableGitInfo: false

taxonomies:
  tag: tags
  category: categories

outputs:
  home:
    - HTML
    - RSS
    - JSON

languages:
  ru:
    languageName: Русский
    title: Александр Михайлов
    contentDir: content/ru
    weight: 1
    params:
      description: Личный сайт Александра Михайлова

markup:
  goldmark:
    renderer:
      unsafe: true
  highlight:
    style: tango

params:
  author: Александр Михайлов
  defaultTheme: auto
  ShowReadingTime: false
  ShowShareButtons: false
  ShowPostNavLinks: true
  ShowBreadCrumbs: false
  ShowCodeCopyButtons: true
  ShowToc: false

  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    minMatchCharLength: 2
    threshold: 0.4

  socialIcons:
    - name: linkedin
      url: https://www.linkedin.com/in/amihailov/
    - name: telegram
      url: https://t.me/positive_write_channel

menu:
  main:
    - name: Обо мне
      url: /about/
      weight: 1
    - name: Портфолио
      url: /portfolio/
      weight: 2
    - name: Блог
      url: /blog/
      weight: 3
    - name: Резюме
      url: /resume/
      weight: 4
    - name: Контакты
      url: /contact/
      weight: 5
    - name: Поиск
      url: /search/
      weight: 6

server:
  bind: "0.0.0.0"
```

### Шаг 4 — Создание структуры папок

```powershell
cd C:\Site\my-site
New-Item -ItemType Directory -Path content\ru\about
New-Item -ItemType Directory -Path content\ru\portfolio
New-Item -ItemType Directory -Path content\ru\blog
New-Item -ItemType Directory -Path content\ru\resume
New-Item -ItemType Directory -Path content\ru\contact
New-Item -ItemType Directory -Path content\ru\search
New-Item -ItemType Directory -Path static\images
New-Item -ItemType Directory -Path static\files
New-Item -ItemType Directory -Path assets\css\extended
New-Item -ItemType Directory -Path layouts
```

Скопировать фото в `static\images\photo_amikhaylov.jpg`.

### Шаг 5 — Первый запуск

```powershell
hugo server
```

Открыть **http://127.0.0.1:1313**

---

## Этап 3 — Контент

### Типы файлов

| Тип | Когда использовать |
|-----|-------------------|
| `index.md` | Одиночная страница: About, Portfolio, Resume, Contact |
| `_index.md` | Раздел со списком записей: Blog, главная |

### Главная страница

Файл `content/ru/_index.md`:

```markdown
---
title: "Александр Михайлов"
---
```

Содержимое главной задаётся через кастомный шаблон `layouts/index.html`.

### Блог

Файл `content/ru/blog/_index.md`:

```markdown
---
title: "Блог"
---
```

> Не добавлять `layout: "single"` — иначе список постов не отобразится.

Каждый пост — отдельный файл в `content/ru/blog/`:

```markdown
---
title: "Заголовок поста"
date: 2026-03-22
tags: ["тег1", "тег2"]
description: "Краткое описание"
---

Текст поста...
```

### Резюме

Файл `content/ru/resume/index.md` — кнопка PDF добавляется HTML-тегом после front matter:

```markdown
---
title: "Резюме"
layout: "single"
---

<a href="/files/resume.pdf" download class="resume-download-btn">Скачать резюме (PDF)</a>
```

PDF кладётся в `static/files/resume.pdf`.

### Поиск

Файл `content/ru/search/index.md`:

```markdown
---
title: "Поиск"
layout: "search"
placeholder: "Поиск по сайту..."
---
```

---

## Этап 4 — Кастомизация

### Кастомная главная страница

Файл `layouts/index.html`:

```html
{{- define "main" }}
<div class="home-container">
  <div class="home-text">
    <h1 class="home-title">Привет, я Александр Михайлов</h1>
    <p class="home-subtitle">Управляю командой технических писателей, пишу и редактирую тексты.</p>
    <div class="home-social">
      <a href="https://www.linkedin.com/in/amihailov/" target="_blank" rel="noopener" aria-label="LinkedIn">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6z"></path><rect x="2" y="9" width="4" height="12"></rect><circle cx="4" cy="4" r="2"></circle></svg>
      </a>
      <a href="https://t.me/positive_write_channel" target="_blank" rel="noopener" aria-label="Telegram">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="22" y1="2" x2="11" y2="13"></line><polygon points="22 2 15 22 11 13 2 9 22 2"></polygon></svg>
      </a>
    </div>
  </div>
  <div class="home-photo">
    <img src="/images/photo_amikhaylov.jpg" alt="Александр Михайлов">
  </div>
</div>
{{- end }}
```

### Кастомные стили

Файл `assets/css/extended/custom.css`:

```css
/* Кнопка скачивания резюме */
.resume-download-btn {
    display: inline-block;
    margin-bottom: 2rem;
    padding: 0.6rem 1.4rem;
    background: var(--primary);
    color: var(--theme) !important;
    border-radius: 6px;
    font-size: 0.95rem;
    font-weight: 500;
    text-decoration: none !important;
    transition: opacity 0.2s;
}

.resume-download-btn:hover {
    opacity: 0.85;
}

/* Главная страница */
.home-container {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    gap: 2rem;
    box-sizing: border-box;
    width: 1052px;
    max-width: 100vw;
    margin-top: 4rem;
    margin-bottom: 4rem;
    margin-left: calc(-1 * (1052px - 100%) / 2);
    padding: 0;
}

.home-text {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.home-title {
    font-size: 2rem;
    font-weight: 700;
    line-height: 1.2;
    margin: 0;
}

.home-subtitle {
    font-size: 1.1rem;
    font-weight: 400;
    margin: 0;
    color: var(--secondary);
}

.home-social {
    display: flex;
    gap: 1rem;
    margin-top: 0.5rem;
}

.home-social a {
    color: var(--primary);
    opacity: 0.7;
    transition: opacity 0.2s;
}

.home-social a:hover {
    opacity: 1;
}

.home-photo img {
    width: 392px;
    height: auto;
    border-radius: 8px;
    display: block;
}

/* Адаптив */
@media (max-width: 768px) {
    .home-container {
        flex-direction: column-reverse;
        margin: 2rem auto;
        width: 100%;
    }
    .home-photo img {
        width: 100%;
    }
    .home-text {
        text-align: center;
        align-items: center;
    }
}
```

> Значение `1052px` — реальный `max-width` навбара PaperMod. Проверить:
> ```javascript
> getComputedStyle(document.querySelector('nav.nav')).maxWidth
> ```

---

## Этап 5 — Деплой на GitHub Pages

### Шаг 1 — Создание репозитория

Создать на https://github.com/new:
- **Repository name:** `amihailov76.github.io`
- **Visibility:** Public
- Галочки не ставить

### Шаг 2 — Обновление baseURL

```yaml
baseURL: https://amihailov76.github.io/
```

### Шаг 3 — Первый пуш

```powershell
cd C:\Site\my-site
git remote add origin https://github.com/amihailov76/amihailov76.github.io.git
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

### Шаг 4 — GitHub Actions

Создать `.github/workflows/hugo.yaml`:

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: "0.158.0"
          extended: true
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build
        run: hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Шаг 5 — Включение GitHub Pages

Открыть https://github.com/amihailov76/amihailov76.github.io/settings/pages → **Build and deployment → Source → GitHub Actions**.

### Шаг 6 — Пуш workflow

```powershell
git add .
git commit -m "Add GitHub Actions workflow"
git push
```

### Рабочий процесс

```powershell
git add .
git commit -m "Описание изменений"
git push
# Сайт обновится через ~1-2 минуты
```

---

## Структура проекта

```
my-site/
├── .github/
│   └── workflows/
│       └── hugo.yaml              # Автодеплой
├── assets/
│   └── css/
│       └── extended/
│           └── custom.css         # Кастомные стили
├── content/
│   └── ru/
│       ├── _index.md              # Главная
│       ├── about/index.md
│       ├── portfolio/index.md
│       ├── blog/
│       │   ├── _index.md
│       │   └── post-name.md       # Посты блога
│       ├── resume/index.md
│       ├── contact/index.md
│       └── search/index.md
├── layouts/
│   └── index.html                 # Шаблон главной
├── static/
│   ├── images/
│   │   └── photo_amikhaylov.jpg
│   └── files/
│       └── resume.pdf
├── themes/
│   └── PaperMod/
└── hugo.yaml
```
