# Odgovori na najčešća pitanja

## Problem sa verzijom LocalDB-a

Ukoliko instalirate LocalDB, ali i pored toga što je instalirana verzija sa sledećeg linka: https://go.microsoft.com/fwlink/?LinkID=866658, prilikom izvršavanja komande `sqllocaldb info` dobijate informaciju da se radi o verziji `13.x.xxxx.x`, problem je verovatno u tome što postoji još jedna instalirana verzija, koju je pribavio Visual Studio Installer.

Postoji nekoliko rešenja za ovaj problem:

1. Moguće je koristiti komandu `sqllocaldb` sa sledećim parametrima (`sqllocaldb create Test 15.0 -s`):
    - Prvi deo komande je isti kao i kreiranje u slučaju da imate samo verziju 15.0
    - 15.0 omogućava kreiranje instance korišćenjem isključivo verzije `15.0`. Ukoliko ova verzija nije instalirana, prikazaće se greška
    - `-s` na kraju može da se koristi u bilo kom slučaju kada ne želite da nakon `sqllocaldb create Naziv` naredbe koristite `sqllocaldb start Naziv` komandu, već želite da se ova instanca automatski i pokrene

2. Moguće je obrisati verziju `13.x`, ukoliko se ona **ne koristi u nekom od prethodnih projekata**. Nakon što je ova verzija obrisana, izvršenje komande radi sa verzijom novijom verzijom, ukoliko je samo ona preostala.

3. Update Visual Studio Installer-a će, u nekim slučajevima rešiti isti problem, tako što će instalirati najnoviju verziju. Ukoliko to ne reši problem, moguće je pronaći `sqllocaldb` verziju koja se nalazi u PATH promenjivoj (`PATH`komanda u komandnoj liniji prikazuje ovu promenjivu, a unosi mogu da budu pronađeni u `System Properties` dijalogu). Obično je putanja nalik na: `C:\Program Files\Microsoft SQL Server\ddd\Tools\Binn\` gde ddd može da bude `130`, `140` ili `150`. Ovi brojevi predstavljaju verziju, tako da je potrebno da na prvom mestu, odozgo na dole bude verzija `15.0` (`150`).

4. Moguće je koristiti i verziju `13.0`. Jedini problem koji može da se javi je kod nekih starijih verzija, koje imaju grešku u putnji do `.mdf` datoteke. Ukoliko Vam se pojavi greška u kojoj se pominje da je nemoguće pristupiti (`Access is denied.`) datoteci `C:\Users\KorisnikNazivBaze.mdf` u kome nema `\` karaktera između naziva korisnika i baze, onda je neophodno instalirati noviju verziju.

## Problem sa verzijom .NET-a

Jako retko, može da se javi i problem prilikom pokretanja bilo koje `dotnet` komande. Problem, naravno, može da se desi i kada nije instaliran ni jedan `.NET SDK` (verzija `5.x`) konkretno u našem slučaju.

Ipak, moguće je da se ovaj problem javi i ukoliko je adekvatna verzija već instalirana i to u 2 slučaja:

1. Ukoliko je putem Visual Studio Installer-a instalirana druga verzija, npr. `3.xx`. U ovom slučaju najlakše rešenje je da se instalira update za Visual Studio. Danas, kada se pojavio i `.NET 6`, moguće je da će ovom prilikom da vam bude instaliran i on, a zaobiđena odgovarajuća instalacija.
Ukoliko želite da budete sigurni da ste instalirali odgovoarajuću verziju `.NET 5` framework-a, najbolje je preuzeti je sa sajta, instalirati i podesiti da njegova putanja u `PATH` promenjivoj bude na prvom mestu (pogledajte: Problem sa verzijom LocalDB-a).

2. Takođe, ukoliko je verzija `5.xx` instalirana, a ona nije osnovna prilikom pozivanja komande za kreiranje projekta, moguće je koristiti i argument: `dotnet new webapi -f net5.0`. Ovaj argument obezbeđuje da svaki projekat bude kreiran korišćenjem .NET verzije koju ste uneli.

3. Još jedan problem koji jako retko može da se javi, je da ste ranije imali instaliranu 32-bitnu verziju .NET-a, a onda je izbrisali, pa instalirali 64-bitnu (ili obrnuto). U ovom slučaju je neophodno proveriti `PATH` promenjivu i utvrditi koja je prva putanja koja sadrži `dotnet.exe` fajl. Ukoliko to nije verzija `5.xx`, onda možete da joj zamenite mesta sa verzijom 5 ili da je izbrišete iz `PATH` promenjive, ukoliko **Vam nije potrebna**.

## Pogrešna verzija Nuget paketa

Ukoliko imate problema sa verzijom nuget paketa (potrebna Vam je verzija `5.xx`), a instalira se verzija starija ili novija od nje, onda je moguće koristiti sledeću komandu:
- `dotnet add package Microsoft.EntityFrameworkCore.Proxies --version 5.0.12` (primer)

Na sajtu je moguće pronaći sve moguće verzije, i uglavnom je svaka od njih vezana za verziju `dotnet SDK-a`.

https://www.nuget.org/

## Pogrešna verzija MSBuild alata

Ukoliko, prilikom pokretanja Visual Studio Code-a naiđete na problem sa OmniSharp paketom, koji ne prikazuje Intellisense ili bilo koje druge funkcionalnosti unutar Visual Studio Code-a, problem može da bude verzija .NET-a, što smo obradili u prethodnim pitanjima, ali takođe i problem sa verzijom MSBuild alata. Ukoliko imate instaliran Visual Studio, on u sebi sadrži ovaj alat, koji se koristi za kompilaciju. Visual Studio, ukoliko je instaliran pre Visual Studio Code-a, se nalazi iznad putanje do poslednje verzije .NET-a koja je instalirana u PATH promenjivoj, pa se njegova verzija alata i koristi.

Problem može da se reši na 2 načina:

1. Instalirajte najnoviji update kroz Visual Studio Installer ili
2. U root direktorijumu projekta kreirajte `omnisharp.json` datoteku sa sledećim sadržajem:

```json
{
    "msbuild":
    {
        "useBundledOnly": true
    }
}
```

Bilo koji od ova 2 pristupa bi trebalo da reši problem.