# Tutorial - Tabelas Particionadas Postgres

## Por que?

Particionar refere-se à divisão do que é logicamente uma única tabela em pequenos pedaços. Tem os seguintes benefícios:

- O desempenho da consulta pode ser dramaticamente melhorado em certas situações, particularmente quando a maioria das linhas da tabela com intenso acesso se encontram numa única partição ou num pequeno número de partições. 

    A partição substitui as colunas principais dos índices, reduzindo o tamanho do índice e tornando mais provável que as partes mais utilizadas dos índices caibam na memória.

- Quando se consulta ou atualiza o acesso a uma grande percentagem de uma única partição, o desempenho pode ser melhorado, tirando partido da verificação sequencial dessa partição em vez de se utilizar um índice e leituras de acesso aleatórias espalhadas por toda a tabela.

- Os dados raramente utilizados podem ser migrados para meios de armazenamento mais baratos e mais lentos.

## Tipos de partição:

- Range Partitioning

    A tabela é dividida em "intervalos" definidos por uma coluna chave ou conjunto de colunas, sem sobreposição entre os intervalos de valores atribuídos a diferentes partições. Por exemplo, pode dividir-se por intervalos de datas, ou por intervalos de identificadores para determinados objectos de negócio. Os limites de cada intervalo são entendidos como sendo inclusivos no extremo inferior e exclusivos no extremo superior. Por exemplo, se o intervalo de uma partição for de 1 a 10, e o próximo intervalo for de 10 a 20, então o valor 10 pertence à segunda partição e não à primeira.

- List Partitioning

    A tabela é dividida listando explicitamente quais os valores chave que aparecem em cada partição.

- Hash Partitioning

    A tabela é particionada especificando um módulo e um resto para cada partição. Cada partição manterá as linhas para as quais o valor hash da chave da partição dividido pelo módulo especificado produzirá o resto especificado.


## Prática

- Script Comum
     
    > CREATE TABLE "Album"
    (
        "AlbumId" BIGSERIAL,
        "Title" VARCHAR(160) NOT NULL,
        "ArtistId" INT NOT NULL,
        "launchDate" date
    )

    > CREATE INDEX launchDate_idx ON "Album"("launchDate");

    > INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (1, 'For Those About To Rock We Salute You', 1, '1981-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (2, 'Balls to the Wall', 2, '1983-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (3, 'Restless and Wild', 2,'1983-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (4, 'Let There Be Rock', 1, '1987-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (5, 'Big Ones', 3, '1984-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (6, 'Jagged Little Pill', 4, '1995-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (7, 'Facelift', 5, '1989-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (8, 'Warner 25 Anos', 6, '2001-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (9, 'Plays Metallica By Four Cellos', 7, '1996-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (10, 'Audioslave', 8, '2001-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (11, 'Out Of Exile', 8, '2005-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (12, 'BackBeat Soundtrack', 9,'1996-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (13, 'The Best Of Billy Cobham', 10, '2001-01-01');

    > INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (14, 'Free Spirit', 11, '2019-01-01');
    INSERT INTO "Album" ("AlbumId", "Title", "ArtistId", "launchDate") VALUES (15, 'Suncity', 11, '2018-01-01');


- Com partição by RANGE
    > CREATE TABLE "Album"
    (
    "AlbumId" BIGSERIAL,
    "Title" VARCHAR(160) NOT NULL,
    "ArtistId" INT NOT NULL,
    "launchDate" date
    ) PARTITION BY RANGE ("launchDate");

- É necessário criar uma partição DEFAULT pois uma tabela particionada é como se fosse uma "tabela abstrata", precisa de uma implementação para podermos começar a popular com dados.

- Com partição Default:
    > CREATE TABLE "album_part_default"
    PARTITION OF "Album" DEFAULT;


## Fonte:

https://www.postgresql.org/docs/10/ddl-partitioning.html
