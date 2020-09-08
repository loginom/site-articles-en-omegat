## site-articles

### Назначение

Репозиторий предназначен для перевода отдельных статей на сайт с помощью [OmegaT 4.3.2](https://omegat.org/)

### Особенности проекта

1. **Ветка по умолчанию** `master-translated`, а не `master`. Связано с особенностью работы OmegaT. В данном проекта ветка `master` это единственная feature ветка, только с этой веткой работает OmegaT.
    1.1 PR Создаются для слияния `master` в `master-translated`.

### Создание PR

1. Добавляем исходные файлы в папку *source* и *target* и пушим эти изменения в ветку `master-translated` (с веткой не ошибся)
2. Добавляем feature ветку `master` ссылающуюся на `master-translated`. Проект для перевода готов, ссылка https://github.com/loginom/site-articles-en-omegat.git
3. **После первого комита от переводчика*, можно создавать PR для слияния ветки `master` в ветку `master-translated`

### Установка OmegaT

Дистрибутивы программы находятся в папке: *Share\Distrib\Translate\OmegaT*. Нужно использовать их.

1. Устанавливаем *OmegaT_4.3.2_Windows_64_Signed.exe*
2. Устанавливаем плагина `Okapi`. Нужно распаковать файлы *.jar* в папку *c:\Program Files\OmegaT\plugins* из архива *okapiFiltersForOmegaT-1.6-m38-dist.zip*

### Открытие проекта

* В OmegaT выбрать "Проект" --> "Загрузить командный проект"
* Вставляем ссылку для клонирования репозитория https://github.com/loginom/site-articles-en-omegat.git
    * Локальная папка нового проекта, должна появится сама в соответствующем поле. Папка для проекта должна быть пустой.
