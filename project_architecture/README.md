# Project architectuur
Dit project volgt de standaard architectuur die we in meerdere projecten hanteren.

## Mappen structuur
- Alle code staat in `src/`, alle testcases in `tests/`. Scripts nodig tijdens deployments staan in `deploy/`.
- Er is een map `src/main/` met daarin Django settings en urls, uwsgi config en eventuele versie info.
- Overige configuratie files staan in de root (Makefile, Dockerfile, docker-compose bestanden, Jenkinsfile, Readme, requirements files en code-style configuraties).

## Makefile
De Makefile bevat enkele standaard targets:
- `pip-tools`, `install`, `requirements` en `upgrade` om dependencies te installeren en maintainen (zie volgende sectie)
- `migrations` en `migrate` om binnen docker migraties te maken en draaien
- `test` en `pdb` om testcases te draaien, eventueel met pdb om zo een interpreter te krijgen bij eventuele errors.
- `urls` om alle beschikbare (Django) urls te tonen
- `bash` om een bash shell van de app binnen docker te starten
- `build`, `push`, `clean` om de docker image te builden, naar de registry te pushen of alles op te schonen.
- `app` en `dev` om de applicatie lokaal te draaien.

## Docker gebruik
We maken gebruik van een multistage Dockerfile waarmee we drie versies van de image maken:
- `app` welke we op productie draaien
- `dev`, op basis van `app`, waar we tevens de dev requirements installeren. Hiermee kunnen we lokaal makkelijker debuggen
- `test` op basis van `dev` waarin we de testcases draaien.
Om te zorgen dat docker container lokaal toegang heeft tot bestanden in het host-system is het van belang het bestand `docker-compose.override.yml.example` te kopiëren naar `docker-compose.override.yml`, en daarin de `user` attributen aan te passen naar jouw eigen user id. Zodoende heeft de container de juiste rechten om bijvoorbeeld migraties aan te maken en weg te schrijven.

## Dependency management
Zie ook https://github.com/Amsterdam/opdrachten_team_dev/tree/feature/project_architecture/dependency_management.

Het project bevat twee bestanden die als bron voor de dependencies dienen:
- requirements.in
- requirements_dev.in
De eerste bevat requirements die nodig zijn om de applicatie in productie te draaien, de tweede bevat extra development requirements welke bijvoorbeeld nodig zijn om testcases uit te voeren.
Beide bestanden bevatten in de regel geen pinned dependencies (bijvoorbeeld Django==3.0.1), tenzij om duidelijke redenen een bepaalde versie specifiek nodig is in het project, en deze niet geüpgrade mag worden naar een latere versie.
Door middel van enkele Make targets maintainen we de requirements:
- `make install` installeert de bestaande requirements zoals gedefinieerd in requirements.txt en requirements_dev.txt.
- `make requirements` leest de `.in` bestanden in en maakt op basis daarvan de `requirements[_dev].txt` bestanden. Deze bevatten altijd de laatste versies van de dependencies, tenzij gepinned in de `requirements[_dev].in` bestanden.
- `make upgrade` voert `make requirements` uit, en installeert deze direct lokaal in de virtualenv.
Door deze werkwijze gelijkmatig in onze projecten door te voeren zijn we in staat in korte tijd vele projecten automatisch te upgraden. `make upgrade && make test` is voldoende om alle requirements te upgraden en te testen of alles naar behoren werkt.

## Deployments
Zoals in de Jenkinsfile gedefinieerd hanteren we de volgende deployment strategie:
- Alle pushes naar `master` deployen we naar acceptatie.
- Alle tags die aan het semver formaat voldoen (x.y.z*) deployen we naar productie.
Jenkins scant periodiek de repository en deployed automatisch.
