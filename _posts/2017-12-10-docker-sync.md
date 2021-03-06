---
title: docker-sync
date: 2017-12-10
tags: docker sync filesystem osxfs performance
---

В [обзорной статье](ссылка на предыдущий пост) об **osxfs** в **Docker for Mac** я упоминал, что имеются существенные проблемы с производительность. Одним из способов решения данной проблемы является гем [docker-sync](https://github.com/EugenMayer/docker-sync). Давайте разберемся, что же он такого делает, чтобы тома не тормозили.

docker-sync прекрасен тем, что он предоставляет унивирсальный синхронизирующий интерфейс для Linux, MacOS и Windows. В Linux это просто использование родных (нативных) точек монтирования, для MacOS же решение немного сложнее и носит название **native_osx**. т.к. не тянет за собой никаких дополнительный зависимостей. Ну если быть точнее, то в docker-sync существует несколько стратегий (чистый Unison и rsync) синхронизации данных, но самой быстрой является native_osx.

![native_osx](https://raw.githubusercontent.com/EugenMayer/docker-sync/master/doc/native_osx.png)

Чтобы невилировать медленную синхронизацию тома на базе osxfs, docker-sync добавляет дополнительный контейнер, призваный **асинхронно** реплицировать данные внутри себя из целового тома через [Unison](http://www.cis.upenn.edu/~bcpierce/unison/). Таким образом мы работает на нативной скорости оставаясь в контексте Linux Docker Host, оставляя всю медленную синхронизацию на плечи Unison. Конечно при таком подходе есть шанс, что данные между файловыми системами разойдутся во времени, т.е. будут согласованы в конечном счете (когда-нибудь). Но при разработке это не сильно критично, более того Unison будет стараться сглаживать эти моменты, как я понимаю. 
