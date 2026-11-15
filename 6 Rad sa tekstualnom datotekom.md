# Rad sa tekstualnom datotekom

Programiranje, IV razred · Visual C# 2010 Express · Klasa File

## Zašto datoteka

Sve što program drži u promenljivama i kontrolama nestaje kada se zatvori; tekstualna datoteka (text file) čuva podatke i posle zatvaranja programa.

Ako u ListBox dodate deset učenika i zatvorite program, sutra je lista prazna. Ako iste podatke upišete u datoteku, program ih pri sledećem pokretanju ponovo učita.

Na ovom času pravimo aplikaciju **Ocene i prosek**:

1. upisujete ime učenika i ocenu;
2. program dopisuje taj podatak u datoteku ocene.txt;
3. pri svakom pokretanju program čita datoteku, prikazuje sve ocene i računa prosek.

Za rad sa datotekom koristimo klasu **File** (iz imenskog prostora System.IO). Njene metode u jednom redu koda pročitaju ili upišu celu datoteku.

## Teorija

### Kako izgleda datoteka ocene.txt

Svaki učenik je jedan red. Ime i ocena su razdvojeni znakom ; (tačka-zarez):

```text
Petrović Marko;5
Jovanović Ana;4
Nikolić Ivan;3
```

Taj znak zovemo **separator**. Program zna da je sve pre znaka ; ime, a sve posle njega ocena. Zato ime ne sme da sadrži znak ;.

### Metode klase File koje koristimo

| Metoda | Šta radi | Ako datoteka ne postoji |
| --- | --- | --- |
| File.Exists(ime) | vraća true ako datoteka postoji | vraća false |
| File.ReadAllLines(ime, Encoding.UTF8) | čita datoteku i vraća niz redova (string\[\]) | greška |
| File.ReadAllText(ime, Encoding.UTF8) | čita celu datoteku kao jedan tekst | greška |
| File.AppendAllText(ime, tekst, Encoding.UTF8) | dopisuje tekst na kraj datoteke | pravi novu datoteku |
| File.WriteAllText(ime, tekst, Encoding.UTF8) | briše stari sadržaj i upisuje novi tekst | pravi novu datoteku |
| File.WriteAllLines(ime, niz, Encoding.UTF8) | briše stari sadržaj i upisuje niz, svaki element u svom redu | pravi novu datoteku |

**Razlika koju treba zapamtiti:** WriteAllText **zamenjuje** ceo sadržaj datoteke, a AppendAllText **dodaje** na kraj. Za dodavanje jednog učenika koristimo AppendAllText, da ne bismo obrisali ostale.

**Encoding.UTF8** kaže programu kako da zapiše slova. Bez njega se č, ć, š, ž i đ mogu pogrešno prikazati.

### Deljenje reda metodom Split

Metoda Split deli tekst na delove na mestu separatora i vraća niz:

```csharp
string red = "Petrović Marko;5";
string[] delovi = red.Split(';');   // delovi[0] = "Petrović Marko", delovi[1] = "5"
int ocena = int.Parse(delovi[1]);   // tekst "5" pretvaramo u broj 5
```

### Zašto try–catch

Rad sa datotekom može da ne uspe iz razloga koje program ne kontroliše: datoteka je otvorena u drugom programu, nema dozvole za upis, u datoteci je red bez znaka ;. Zato svaki rad sa datotekom stavljamo u blok try–catch i u catch prikazujemo poruku, umesto da se program sruši.

## Aplikacija Ocene i prosek korak po korak

### Korak 1 – Forma

Napravite novi projekat Windows Forms Application pod imenom **OceneProsek**.

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | Form1 | Text: Ocene i prosek, Size: 520; 400, StartPosition: CenterScreen |
| Label | lblIme | Text: Ime i prezime: |
| TextBox | txtIme | Width: 180 |
| Label | lblOcena | Text: Ocena (1–5): |
| TextBox | txtOcena | Width: 40 |
| Button | btnDodaj | Text: Dodaj |
| ListBox | lstOcene | Size: 260; 220 |
| Label | lblBroj | Text: Broj učenika: 0 |
| Label | lblProsek | Text: Prosek: nema ocena, Font: Bold |
| Button | btnObrisiSve | Text: Obriši sve |

Izaberite formu i u Properties postavite **AcceptButton: btnDodaj**, da taster Enter dodaje ocenu.

### Korak 2 – using System.IO i ime datoteke

Otvorite kod (F7). Na vrh datoteke, uz ostale using redove, dodajte:

```csharp
using System.IO;
```

U klasu Form1, iznad konstruktora, dodajte polje sa imenom datoteke:

```csharp
// Datoteka se nalazi pored programa (u fascikli bin\Debug)
private string datoteka = "ocene.txt";
```

Ime bez putanje znači: datoteka je u istoj fascikli u kojoj je program.

### Korak 3 – Čitanje datoteke i prosek

Ispod konstruktora dodajte metodu UcitajOcene. Ona čita sve redove, puni listu i računa prosek:

```csharp
private void UcitajOcene()
{
    lstOcene.Items.Clear();

    if (!File.Exists(datoteka))
    {
        lblBroj.Text = "Broj učenika: 0";
        lblProsek.Text = "Prosek: nema ocena";
        return;
    }

    try
    {
        string[] redovi = File.ReadAllLines(datoteka, Encoding.UTF8);
        int zbir = 0;
        int broj = 0;

        foreach (string red in redovi)
        {
            if (red.Trim() == "")
            {
                continue;   // prazan red preskačemo
            }

            string[] delovi = red.Split(';');   // "Petrović Marko;5"
            string ime = delovi[0];
            int ocena = int.Parse(delovi[1]);

            lstOcene.Items.Add(ime + " – " + ocena);
            zbir = zbir + ocena;
            broj++;
        }

        lblBroj.Text = "Broj učenika: " + broj;
        if (broj > 0)
        {
            double prosek = (double)zbir / broj;
            lblProsek.Text = "Prosek: " + prosek.ToString("0.00");
        }
        else
        {
            lblProsek.Text = "Prosek: nema ocena";
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Datoteka nije pročitana.\n" + ex.Message, "Greška",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
```

Dvoklikom na prazan deo forme napravite metodu za obradu događaja Form1\_Load i u njoj pozovite UcitajOcene:

```csharp
private void Form1_Load(object sender, EventArgs e)
{
    UcitajOcene();
}
```

**Objašnjenje**

| Deo koda | Značenje |
| --- | --- |
| if (!File.Exists(datoteka)) | pri prvom pokretanju datoteka još ne postoji; tada samo ispišemo da nema ocena |
| foreach (string red in redovi) | prolazi kroz sve redove datoteke, jedan po jedan |
| (double)zbir / broj | pretvaramo zbir u realan broj; bez toga bi 13 / 3 dalo 4, a ne 4,33 |
| prosek.ToString("0.00") | ispisuje broj sa tačno dve decimale |

### Korak 4 – Dodavanje ocene u datoteku

Dvoklikom na btnDodaj napravite metodu za obradu događaja:

```csharp
private void btnDodaj_Click(object sender, EventArgs e)
{
    string ime = txtIme.Text.Trim();
    int ocena;

    if (ime == "" || ime.Contains(";"))
    {
        MessageBox.Show("Upišite ime i prezime (bez znaka ;).", "Unos",
            MessageBoxButtons.OK, MessageBoxIcon.Warning);
        txtIme.Focus();
        return;
    }

    if (!int.TryParse(txtOcena.Text, out ocena) || ocena < 1 || ocena > 5)
    {
        MessageBox.Show("Ocena mora biti ceo broj od 1 do 5.", "Unos",
            MessageBoxButtons.OK, MessageBoxIcon.Warning);
        txtOcena.Focus();
        txtOcena.SelectAll();
        return;
    }

    try
    {
        // Dopisuje jedan red na kraj datoteke; ako datoteka ne postoji, pravi je
        string red = ime + ";" + ocena;
        File.AppendAllText(datoteka, red + Environment.NewLine, Encoding.UTF8);
    }
    catch (Exception ex)
    {
        MessageBox.Show("Ocena nije upisana.\n" + ex.Message, "Greška",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
        return;
    }

    txtIme.Clear();
    txtOcena.Clear();
    txtIme.Focus();
    UcitajOcene();   // ponovo čitamo datoteku: lista i prosek su ažurni
}
```

**Environment.NewLine** je prelazak u novi red. Bez njega bi se svi učenici upisali u isti red datoteke.

### Korak 5 – Brisanje svih ocena

Dvoklikom na btnObrisiSve:

```csharp
private void btnObrisiSve_Click(object sender, EventArgs e)
{
    DialogResult odgovor = MessageBox.Show("Obrisati sve ocene iz datoteke?", "Brisanje",
        MessageBoxButtons.YesNo, MessageBoxIcon.Question);
    if (odgovor == DialogResult.No)
    {
        return;
    }

    try
    {
        // Upisuje prazan tekst preko starog sadržaja
        File.WriteAllText(datoteka, "", Encoding.UTF8);
    }
    catch (Exception ex)
    {
        MessageBox.Show("Datoteka nije obrisana.\n" + ex.Message, "Greška",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    UcitajOcene();
}
```

Ovde namerno koristimo WriteAllText: on zamenjuje ceo sadržaj, pa upisivanjem praznog teksta datoteka postaje prazna.

## Gde je datoteka

Kada program pokrenete iz Visual Studio-a (F5), on radi iz fascikle **bin\\Debug** vašeg projekta, pa se tu nalazi i ocene.txt.

Da biste je videli: u Solution Explorer-u kliknite na dugme **Show All Files**, rasklopite bin → Debug i dvoklikom otvorite ocene.txt. Datoteku možete otvoriti i u Beležnici (Notepad) iz Windows Explorer-a: Dokumenti → Visual Studio 2010 → Projects → OceneProsek → OceneProsek → bin → Debug.

## Isprobajte

1. Pokrenite program: lista je prazna, piše „Prosek: nema ocena“.
2. Dodajte tri učenika sa ocenama 5, 4 i 3: prosek je 4,00.
3. Zatvorite program i ponovo ga pokrenite: sva tri učenika su i dalje u listi.
4. Otvorite ocene.txt: tri reda oblika Ime;ocena.
5. Probajte ocenu 7 i ocenu „abc“: program prikazuje upozorenje i ništa ne upisuje.
6. Obriši sve → Da: lista je prazna, a datoteka postoji, ali je prazna.

## Česte greške

| Šta vidite | Uzrok | Rešenje |
| --- | --- | --- |
| File je podvučeno crveno | nedostaje using System.IO | dodajte `using System.IO;` na vrh Form1.cs |
| Posle dodavanja ostane samo poslednji učenik | upotrebljen je WriteAllText umesto AppendAllText | za dodavanje koristite AppendAllText |
| U datoteci su svi učenici u jednom redu | nedostaje Environment.NewLine | upisujte `red + Environment.NewLine` |
| Prosek 4 umesto 4,33 | deljenje dva cela broja | `(double)zbir / broj` |
| Slova č, ć, š su pogrešno prikazana | datoteka je pisana bez Encoding.UTF8, ili ručno snimljena u drugom kodiranju | i za čitanje i za upis navedite Encoding.UTF8 |
| Poruka „Index was outside the bounds of the array“ | u datoteci postoji red bez znaka ; (npr. ručno dopisan) | ispravite red u datoteci u oblik Ime;ocena |
| Poruka „Input string was not in a correct format“ | posle znaka ; nije broj | ispravite red u datoteci |
| Lista se ne popuni pri pokretanju | UcitajOcene nije pozvana u Form1\_Load, ili Load nije povezan | Form1\_Load napravite dvoklikom na formu |

## Rezime

- Tekstualna datoteka čuva podatke i posle zatvaranja programa.
- Za rad sa datotekom treba `using System.IO;`, a svaki rad sa datotekom ide u try–catch.
- File.Exists proverava da li datoteka postoji; ReadAllLines čita sve redove u niz.
- AppendAllText dodaje na kraj, a WriteAllText zamenjuje ceo sadržaj.
- Red Ime;ocena delimo metodom Split(';'); tekst u broj pretvaramo sa int.Parse.
- Uvek navodimo Encoding.UTF8, da bi naša slova bila ispravna.

## Pitanja za samoproveru

1. Zašto su podaci iz ListBox-a izgubljeni posle zatvaranja programa, a podaci iz datoteke nisu?
2. Koja je razlika između AppendAllText i WriteAllText? Koju koristite za dodavanje jednog učenika i zašto?
3. Šta vraća red.Split(';') za red „Jovanović Ana;4“?
4. Zašto pišemo (double)zbir / broj, a ne zbir / broj?
5. Gde se nalazi datoteka ocene.txt kada program pokrenete iz Visual Studio-a?

## Zadaci

Svi zadaci nadograđuju aplikaciju Ocene i prosek. Pre izmena napravite kopiju projekta.

### Zadatak 1 – Najviša i najniža ocena \[OSNOVNI\] (na času)

Dodajte oznake lblNajvisa i lblNajniza. Posle učitavanja datoteke one prikazuju najvišu i najnižu ocenu, na primer „Najviša ocena: 5“. Kada nema ocena, prikazuju crticu.

*Pomoć:* pre petlje postavite najvisa = 1 i najniza = 5, a u petlji ih uporedite sa svakom ocenom.

### Zadatak 2 – Broj odličnih \[OSNOVNI\] (domaći)

Dodajte oznaku lblOdlicni koja pokazuje koliko učenika ima ocenu 5.

### Zadatak 3 – Prikaži datoteku \[OSNOVNI\] (domaći)

Dodajte dugme btnPrikazi koje ceo sadržaj datoteke ocene.txt prikazuje u poruci (MessageBox), tačno onako kako je zapisan. Ako datoteka ne postoji, prikažite poruku „Datoteka još ne postoji.“

*Pomoć:* File.ReadAllText.

### Zadatak 4 – Brisanje izabranog učenika \[SREDNJI\] (domaći)

Dodajte dugme btnObrisiIzabranog koje iz datoteke briše učenika izabranog u listi. Ako nije izabran nijedan, prikažite poruku.

*Pomoć:* pročitajte sve redove u List\<string> (bez praznih redova), uklonite element na mestu lstOcene.SelectedIndex i upišite ostatak sa File.WriteAllLines.

### Zadatak 5 – Izveštaj \[SREDNJI\] (domaći)

Dodajte dugme btnIzvestaj koje u novu datoteku izvestaj.txt upisuje naslov IZVEŠTAJ O OCENAMA, današnji datum, broj učenika i prosek, svaki podatak u svom redu. Ako datoteka već postoji, zamenjuje se.

*Pomoć:* DateTime.Now.ToString("dd.MM.yyyy.") daje današnji datum kao tekst.

### Zadatak 6 – Pretraga \[SREDNJI\] (domaći)

Dodajte polje txtPretraga i dugme btnPronadji. Program u datoteci traži učenika sa upisanim imenom i prikazuje njegovu ocenu, ili poruku „Učenik nije pronađen.“ Velika i mala slova se ne razlikuju („petrović marko“ nalazi „Petrović Marko“).

*Pomoć:* uporedite ToLower() oba teksta.

### Zadatak 7 – Više ocena po učeniku \[NAPREDNI\] (po želji)

U Beležnici napravite datoteku ocene2.txt u kojoj svaki red ima ime i više ocena, na primer „Petrović Marko;5;4;3“, i snimite je u fasciklu bin\\Debug (u prozoru Save As izaberite Encoding: UTF-8). Dodajte dugme koje učitava tu datoteku i u listi prikazuje prosek svakog učenika („Petrović Marko – 4,00“), a ispod liste prosek odeljenja.

*Pomoć:* ocene su u delovi\[1\], delovi\[2\]... do delovi.Length - 1.

### Zadatak 8 – Raspodela ocena \[NAPREDNI\] (po želji)

Dodajte dugme btnRaspodela i listu lstRaspodela. Program prebrojava koliko ima petica, četvorki, trojki, dvojki i jedinica, prikazuje to u listi („Ocena 5: 3“) i upisuje isti izveštaj u datoteku raspodela.txt.

*Pomoć:* niz `int[] brojaci = new int[6];` i naredba `brojaci[ocena]++;`.
