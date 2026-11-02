# Otvaranje nove forme

Nov 2, 2026 · @Vanja

## Šta učimo

Druga forma se otvara u dva koraka: napravimo objekat forme sa `new`, pa ga prikažemo sa `Show()` ili `ShowDialog()`.

Do sada su vaše aplikacije imale samo jednu formu, Form1. Većina pravih programa ima više prozora: prozor O programu, prozor Podešavanja, prozor za unos podataka. Svaki takav prozor je posebna forma.

Forma koju dodamo u projekat je **klasa** (opis prozora). Da bi se prozor pojavio na ekranu, pravimo **objekat** te klase, isto kao što od jednog plana kuće možemo da sagradimo više kuća.

Na ovom času ćete:

1. dodati novu formu u projekat;
2. otvoriti je klikom na dugme;
3. videti razliku između `Show()` i `ShowDialog()`.

## Primer korak po korak: Dva prozora

Pravimo aplikaciju u kojoj dva dugmeta na glavnoj formi otvaraju istu drugu formu, frmOProgramu, na dva načina.

### Korak 1 – Novi projekat i glavna forma

Napravite novi projekat Windows Forms Application pod imenom **DvaProzora**. Na Form1 postavite kontrole:

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | Form1 | Text: Glavni prozor, StartPosition: CenterScreen |
| Button | btnShow | Text: Otvori sa Show |
| Button | btnShowDialog | Text: Otvori sa ShowDialog |

### Korak 2 – Dodavanje nove forme

1. U prozoru **Solution Explorer** kliknite desnim tasterom na ime projekta (DvaProzora).
2. Izaberite **Add → Windows Form...**
3. U polje Name upišite **frmOProgramu.cs** i kliknite **Add**.

U projektu se pojavila nova forma, sa svojim dizajnerom i svojim kodom, isto kao Form1.

### Korak 3 – Izgled nove forme

Na frmOProgramu postavite kontrole:

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | frmOProgramu | Text: O programu, Size: 300; 180 |
| Label | lblPoruka | Text: Dva prozora, verzija 1.0 |
| Button | btnZatvori | Text: Zatvori |

### Korak 4 – Dugme Zatvori

Na frmOProgramu uradite dvoklik na dugme btnZatvori. Visual Studio napravi metodu za obradu događaja; dopunite je:

```csharp
// frmOProgramu.cs
private void btnZatvori_Click(object sender, EventArgs e)
{
    this.Close();   // zatvara ovu formu
}
```

Reč `this` označava formu u kojoj se kod nalazi, ovde frmOProgramu.

### Korak 5 – Otvaranje forme iz Form1

Vratite se na Form1 (kartica Form1.cs \[Design\]) i uradite dvoklik na svako od dva dugmeta:

```csharp
// Form1.cs
private void btnShow_Click(object sender, EventArgs e)
{
    frmOProgramu prozor = new frmOProgramu();   // 1. napravimo formu
    prozor.Show();                               // 2. prikažemo je
}

private void btnShowDialog_Click(object sender, EventArgs e)
{
    frmOProgramu prozor = new frmOProgramu();
    prozor.ShowDialog();
}
```

**Objašnjenje reda `frmOProgramu prozor = new frmOProgramu();`**

| Deo | Značenje |
| --- | --- |
| frmOProgramu | tip promenljive: ime forme koju smo dodali |
| prozor | ime promenljive; može biti bilo koje |
| new frmOProgramu() | pravi novi prozor u memoriji; on se još ne vidi |

Tek poziv `Show()` ili `ShowDialog()` prikazuje prozor na ekranu.

### Korak 6 – Isprobajte

1. Pokrenite program (F5) i kliknite **Otvori sa Show**. Kliknite tri puta: otvaraju se tri prozora O programu, a glavni prozor i dalje radi.
2. Zatvorite ih. Kliknite **Otvori sa ShowDialog**. Pokušajte da kliknete na glavni prozor: ne reaguje dok ne zatvorite O programu.

Ta razlika je tema sledećeg odeljka.

## Show i ShowDialog

`ShowDialog()` otvara **modalni** prozor: ostatak programa čeka dok se on ne zatvori. `Show()` otvara **nemodalni** prozor: oba prozora rade istovremeno.

|  | ShowDialog() – modalni | Show() – nemodalni |
| --- | --- | --- |
| Glavni prozor dok je drugi otvoren | ne reaguje | radi normalno |
| Kod posle poziva | izvršava se tek kada se prozor zatvori | izvršava se odmah |
| Više kopija istog prozora | ne može | može |
| Primer iz Worda | Save As, Font | Find and Replace |

**Kada koji?** Modalni prozor koristite kada program ne sme da nastavi bez odgovora korisnika (unos podataka, potvrda, O programu). Nemodalni koristite za prozor koji stoji pored glavnog dok korisnik radi (uputstvo, pomoćna lista).

### Proverite sami: kada se izvrši sledeći red?

U obe metode za obradu događaja u Form1, posle reda sa `Show()` odnosno `ShowDialog()`, privremeno dodajte poruku:

```csharp
MessageBox.Show("Red posle poziva je izvršen.");
```

Pokrenite program i probajte oba dugmeta:

- sa **Show** poruka se pojavi odmah, dok je O programu još otvoren;
- sa **ShowDialog** poruka se pojavi tek kada zatvorite O programu.

Posle provere obrišite oba probna reda.

## Česte greške

| Šta vidite | Uzrok | Rešenje |
| --- | --- | --- |
| frmOProgramu je podvučeno crveno | ime forme u kodu nije isto kao u Solution Explorer-u (npr. frmOprogramu, malo p) | prepišite ime tačno, sa istim velikim i malim slovima |
| Klik na dugme ne radi ništa | napisali ste samo `new frmOProgramu();`, bez Show | dodajte `prozor.Show();` ili `prozor.ShowDialog();` |
| Napisali ste `frmOProgramu.Show();` i dobili grešku | Show se poziva nad objektom, ne nad klasom | prvo `new`, pa `prozor.Show();` |
| Dugme Zatvori zatvara ceo program | kod je napisan u Form1 umesto u frmOProgramu, ili stoji `Application.Exit();` | u frmOProgramu napišite `this.Close();` |
| Kod za dugme je napisan, ali se ne izvršava | metoda je prekucana ručno, pa nije povezana sa događajem | obrišite je i napravite je dvoklikom na dugme |

## Rezime

- Nova forma se dodaje u Solution Explorer-u: desni klik na projekat → Add → Windows Form.
- Formu otvaramo u dva koraka: `frmOProgramu prozor = new frmOProgramu();`, pa `prozor.Show();` ili `prozor.ShowDialog();`.
- `ShowDialog()` je modalni prikaz: ostatak programa čeka. `Show()` je nemodalni prikaz: oba prozora rade.
- Forma se zatvara sama sa `this.Close();`.

## Pitanja za proveru

1. Kako se dodaje nova forma u projekat?
2. Šta radi red `frmOProgramu prozor = new frmOProgramu();`? Da li se posle njega prozor vidi?
3. Koja je razlika između `Show()` i `ShowDialog()`?
4. Za prozor Podešavanja, u kome korisnik mora da potvrdi izbor pre nastavka rada, koji biste način prikaza izabrali i zašto?

## Zadatak za vežbu

Napravite projekat **Skola** sa glavnom formom na kojoj su dva dugmeta:

- **Raspored časova** (btnRaspored) otvara formu frmRaspored nemodalno. Na njoj je Label sa tekstom „Ponedeljak: Programiranje, Matematika“ i dugme Zatvori.
- **O školi** (btnOSkoli) otvara formu frmOSkoli modalno. Na njoj je Label sa nazivom i mestom vaše škole i dugme U redu, koje zatvara formu.

Proverite: Raspored može da bude otvoren dva puta i glavni prozor radi; dok je otvoren O školi, glavni prozor ne reaguje.

*Za višu ocenu:* na glavnu formu dodajte meni Pomoć sa stavkom O školi (F1), koja otvara istu formu kao dugme, bez ponovljenog koda.
