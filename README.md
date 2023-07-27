# Блог TinyOps

[https://b.tinyops.ru](https://b.tinyops.ru)

## Как собрать проект

```shell script
rm -rf docs

git clone https://github.com/tinyops-ru/tinyblog-zola-theme.git themes/tinyblog

zola build --output-dir docs --base-url https://b.tinyops.ru
```

Результат будет в папке `docs`. При коммите не удалять файл `CNAME`, его использует GitHub для фичи Page.