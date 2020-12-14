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
Zie https://github.com/Amsterdam/opdrachten_team_dev/tree/feature/project_architecture/dependency_management.

## Deployments
Zoals in de Jenkinsfile gedefinieerd hanteren we de volgende deployment strategie:
- Alle pushes naar `master` deployen we naar acceptatie.
- Alle tags die aan het semver formaat voldoen (x.y.z*) deployen we naar productie.
Jenkins scant periodiek de repository en deployed automatisch.
