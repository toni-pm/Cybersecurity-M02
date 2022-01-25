<!-----
title: "CRYPTO - Competition"
author: "Toni Peraira"
date: "2022-01-11"
version: "1.0"
geometry: left=2.54cm,right=2.54cm,top=2.54cm,bottom=2.54cm
header-right: '\headerlogo'
header-includes:
- '`\newcommand{\headerlogo}{\raisebox{0pt}[0pt]{\includegraphics[width=3cm]{../institut_montilivi.png}}}`{=latex}'
---

pandoc README.md -o Toni_Peraira_CRYPTO_Competition.pdf --from markdown --template eisvogel --listings --pdf-engine=xelatex --toc -s -V toc-title:"Índex"
-->

# CRYPTO - Competition <!-- omit in toc -->

A continuació hi ha un fitxer de passwords d'una empresa real que va tancar les seves portes fa temps a la comarca de girona. 

A partir del moment en què presento aquesta pràctica comença la competició. 

**Com puntuaré?**

La puntuació serà vers el nombre de hashos que aconsegueixis, la intenció és fer un barem en funció dels resultats generals. 

El que més hashos aconsegueixi tindrà la nota més alta. 

Contestar les preguntes i passar el challenge amb el professor pot assegurar-te un 5 tot i no tenir cap hash aconseguit.  

# Índex <!-- omit in toc -->
- [Instal·la l'eina hashcat o demostra que la tens instal·lada i la seva versió](#installa-leina-hashcat-o-demostra-que-la-tens-installada-i-la-seva-versió)
- [Quin tipus de hash hi ha al fitxer hashes.txt?](#quin-tipus-de-hash-hi-ha-al-fitxer-hashestxt)
- [Cerca informació sobre el tipus de hash i descriu-me quant de vulnerable és](#cerca-informació-sobre-el-tipus-de-hash-i-descriu-me-quant-de-vulnerable-és)
- [Com ho faries per petar hashos més ràpid que els demés?](#com-ho-faries-per-petar-hashos-més-ràpid-que-els-demés)
- [Què vol dir que un hash col·lisiona?](#què-vol-dir-que-un-hash-collisiona)
- [Fes servir hashcat per petar el màxim de hashos que puguis](#fes-servir-hashcat-per-petar-el-màxim-de-hashos-que-puguis)

## Instal·la l'eina hashcat o demostra que la tens instal·lada i la seva versió

```console
➜  ~ hashcat --version
v5.1.0
```

## Quin tipus de hash hi ha al fitxer hashes.txt?

Es tracta del tipus **md5crypt**, MD5 (Unix), Cisco-IOS $1$ (MD5). Que correspon al hash-mode 500 del hashcat segons la seva documentació https://hashcat.net/wiki/doku.php?id=example_hashes.

## Cerca informació sobre el tipus de hash i descriu-me quant de vulnerable és

md5crypt és un algoritme de xifrat vulnerable, no perquè l'algoritme criptogràfic sigui feble sinó perquè és molt ràpid d'utilitzar. És a dir, es poden provar moltes possibles contrasenyes en poc temps. A classe hem pogut provar moltes possibilitats fent servir les nostres GPU, amb altres tipus de hash més lents haguessim tardat molt més. 

Podem fer servir el mode benchmark del hashcat per comprovar el hashrate del nostre hardware per cada tipus de hash.

```console
➜  ~ hashcat -b --force
hashcat (v5.1.0) starting in benchmark mode...

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

OpenCL Platform #1: The pocl project
====================================
* Device #1: pthread-Intel(R) Core(TM) i7-3632QM CPU @ 2.20GHz, 4096/13785 MB allocatable, 8MCU

Benchmark relevant options:
===========================
* --force
* --optimized-kernel-enable

Hashmode: 0 - MD5

Speed.#1.........:   246.3 MH/s (32.01ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

Hashmode: 100 - SHA1

Speed.#1.........:   101.0 MH/s (78.31ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

Hashmode: 1400 - SHA2-256

Speed.#1.........: 38680.5 kH/s (51.03ms) @ Accel:1024 Loops:256 Thr:1 Vec:8

Hashmode: 1700 - SHA2-512

Speed.#1.........:  8273.2 kH/s (59.70ms) @ Accel:512 Loops:128 Thr:1 Vec:4

Hashmode: 2500 - WPA-EAPOL-PBKDF2 (Iterations: 4096)

Speed.#1.........:     3544 H/s (55.44ms) @ Accel:512 Loops:256 Thr:1 Vec:8

Hashmode: 1000 - NTLM

Speed.#1.........:   361.4 MH/s (21.97ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

```

```console
hashcat -b -m 500 --force
```

## Com ho faries per petar hashos més ràpid que els demés?

Aconseguir la millor de les targetes gràfiques i el millor dels diccionaris, així com la millor de les regles, per provar un nombre de hashes més gran que els altres en menor temps. És a dir, necessitaria el hashrate més gran possible.

Per intentar ser més ràpid, tenint pistes sobre l'origen dels hashes, crearia un diccionari personalitzat i les regles més comunes. Això és el que he fet, sabent que tenia relació amb Girona i que la majoria de gent té contrasenyes febles, he creat un diccionari amb noms i cognoms catalans i espanyols, així com temes típics d'aquí com el futbol i les motos. He afegit regles típiques com són números al final de cada contrasenya (Peraira93, Marc19, Joel1995) i combinacions de minúscules i majúscules. A mesura que anava trobant contrasenyes anava veient més o menys de quin tipus són i anava creant les regles i el diccionari segons què trobava.

## Què vol dir que un hash col·lisiona?

Una col·lisió de hash és quan dues entrades diferents donen el mateix resultat utilitzant la mateixa funció de hash, quan diferents entrades col·lisionen es diu que són sinònims.

Això passa perquè el conjunt de possibles hash és sempre més gran al nombre d'espais disponibles, el nombre de possibles entrades sempre serà més gran que el nombre de possibles entrades.

## Fes servir hashcat per petar el màxim de hashos que puguis

S'han trobat un total de 180 resultats.

Algunes comandes fetes servir:

```console
hashcat -d 1 -m 500 -a 0 -o cracked.txt hashes.txt -r material/joel.rule material/diccionaris/nombres_masculinos.txt  material/diccionaris/nombres_sin_acentos.txt material/diccionaris/nombres_variados_minusculas.txt material/diccionaris/noms_amb_accents.txt material/diccionaris/nombres_sin_acentos.txt material/diccionaris/noms_sense_accents.txt  -O
```

```console
hashcat -d 1 -m 500 -a 0 -o cracked.txt hashes.txt material/cyclone.txt  -O --force -w 3
```

```console
hashcat -d 1 -m 500 -a 0 -o cracked.txt hashes.txt wordlists/el_diccionari_del_toni.txt -r rules/toni.rule -O
```