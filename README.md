# MadalHind OSM export (Эстония)

Ежедневная выгрузка из OpenStreetMap для карты магазинов madalhind.ee (`/kauplused/`): продуктовые и алкогольные магазины,
торговые центры, аптеки и промтовары, ориентиры (станции, автовокзалы, рынки, управы, больницы, почта, церкви, музеи)
и автоматы приёма тары (taaraautomaadid) — только Эстония, обрезка по официальной границе.

Копия `docs/osm-export-lt` у maza-kaina.lt (а та — [aeronavtik/mazacena-osm](https://github.com/aeronavtik/mazacena-osm))
с эстонскими границей (`ISO3166-1=EE`), тегом `name:et` и сетями: `shop=alcohol` в продуктах (CityAlko, A1000 Market),
электроника (Euronics), спорт (Sportland), стройматериалы; автоматы тары — `vending=bottle_return` и автоматы / пункты
приёма с «taara» или «pandi» в названии.

Зачем: без выгрузки сайт берёт данные OSM из общих серверов Overpass, а они часто перегружены (22.11.2026 днём
overpass-api.de отвечал 502, maps.mail.ru — 504, private.coffee не отвечал). Тогда на карту не попадают сети без своего
источника (Grossi, Meie Toidukaubad, Südameapteek, Euroapteek, Bauhof, K-Rauta, JYSK и др.), торговые центры,
тароматы и ориентиры.

Каждую ночь (00:00 UTC) GitHub Actions:

1. скачивает свежую выгрузку Эстонии с [Geofabrik](https://download.geofabrik.de/europe/estonia.html) (~125 МБ);
2. вырезает Эстонию по границе и оставляет нужные объекты утилитой [osmium](https://osmcode.org/osmium-tool/);
3. `build.php` пишет файлы в формате ответа Overpass API (`food.json`, `other.json`, `places.json`, `meta.json`);
4. публикует их на GitHub Pages.

Если объектов подозрительно мало (food < 500 или other < 200), выгрузка не публикуется, и на Pages остаются вчерашние файлы.

## Настройка (один раз) — репозиторий ещё не создан (22.11.2026)

1. Создайте на GitHub **публичный** репозиторий `madalhind-osm` у пользователя `aeronavtik-rgb` и загрузите в него
   содержимое этой папки, включая `.github/`.
2. **Settings → Pages → Build and deployment → Source: GitHub Actions.**
3. **Actions → OSM export → Run workflow** — первый запуск вручную (около 3 минут).
4. Проверьте, что открывается `https://aeronavtik-rgb.github.io/madalhind-osm/meta.json`.

Сайт ищет выгрузку по этому адресу сам (`OsmData::exportUrl`, настройка `stores.osm_export` его меняет). Пока адрес
не отвечает (или meta.json старше 3 суток), импорт идёт через Overpass раз в неделю; когда выгрузка появится — OSM
обновляется каждую ночь (`mz:stores-import`, 03:15 по Таллину).

Версии actions — как у maza-kaina-osm после 21.11.2026 (checkout v7, configure-pages v6, upload-pages-artifact v5,
deploy-pages v5): со старыми deploy-pages не находил артефакт.
