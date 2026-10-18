# Susret 1 – Materijal za učenike: Konvertor jedinica

Oct 18, 2026 · @Vanja

## Šta pravimo

Za tri časa napravićete aplikaciju „Konvertor jedinica“: upišete broj, izaberete konverziju u meniju i dobijete rezultat. Usput ćete naučiti tri stvari koje ima skoro svaki Windows program.

| Čas | Šta učimo | Šta dodajemo u Konvertor |
| --- | --- | --- |
| 1 | dijalog uz prikaz poruke (MessageBox) | poruke o grešci i upozorenju, pitanje pri izlasku |
| 2 | glavni meni (MenuStrip) | menije Datoteka, Konverzija i Pomoć |
| 3 | pomoćni (kontekstni) meni (ContextMenuStrip) | meni na desni klik na polje za unos |

Konvertor pretvara četiri jedinice: kilometre u milje, stepene Celzijusa u stepene Farenhajta, kilograme u funte i inče u centimetre (dijagonala ekrana telefona ili televizora meri se u inčima).

**Pravilo imenovanja.** Kontrole imenujemo sa prefiksom: btn (dugme), txt (polje za unos), lbl (labela), lst (lista), mnu (stavka menija), cms (pomoćni meni). Traka menija i separatori zadržavaju ime koje im da Visual Studio.

## Čas 1 – Dijalog uz prikaz poruke

Dijalog uz prikaz poruke (MessageBox) je mali prozor koji programu služi da nešto saopšti korisniku ili da ga nešto pita. Dok je poruka otvorena, korisnik ne može da radi ništa drugo u programu: mora prvo da klikne na neko dugme u poruci.

Svaka poruka ima četiri dela: **tekst**, **naslov**, **dugmad** i **ikonicu**. Poruku prikazujemo metodom MessageBox.Show, a parametre uvek pišemo ovim redom:

```csharp
MessageBox.Show("Zdravo!");                       // samo tekst
MessageBox.Show("Zdravo!", "Pozdrav");            // tekst i naslov
MessageBox.Show("Datoteka nije pronađena.", "Greška",
    MessageBoxButtons.OK, MessageBoxIcon.Error);  // tekst, naslov, dugmad, ikonica
```

Novi red unutar teksta poruke pišemo kao \\n, npr. "Prvi red\\nDrugi red".

**Dugmad (MessageBoxButtons)**

| Vrednost | Dugmad u poruci | Kada se koristi |
| --- | --- | --- |
| OK | OK | obaveštenje, greška |
| OKCancel | OK, Cancel | potvrda radnje koja može da se otkaže |
| YesNo | Yes, No | pitanje sa dva odgovora |
| YesNoCancel | Yes, No, Cancel | pitanje uz mogućnost da se odustane |
| RetryCancel | Retry, Cancel | ponovni pokušaj posle neuspeha |

**Ikonice (MessageBoxIcon)**

| Vrednost | Značenje | Primer poruke |
| --- | --- | --- |
| Information | obaveštenje | „Podaci su sačuvani.“ |
| Warning | upozorenje, oprez | „Vrednost ne može biti negativna.“ |
| Error | greška | „Uneto je slovo umesto broja.“ |
| Question | pitanje | „Da li želite da izađete?“ |

**Odgovor korisnika (DialogResult).** Metoda MessageBox.Show vraća koje je dugme kliknuto. Rezultat čuvamo u promenljivoj tipa DialogResult i proveravamo sa if. Vrednost uvek ima ime dugmeta: Yes daje DialogResult.Yes, No daje DialogResult.No, Cancel daje DialogResult.Cancel.

```csharp
DialogResult odgovor = MessageBox.Show("Da li želite da izađete?", "Izlaz",
    MessageBoxButtons.YesNo, MessageBoxIcon.Question);

if (odgovor == DialogResult.Yes)
{
    // korisnik je kliknuo Yes
}
```

**Zatvaranje forme (FormClosing).** Događaj FormClosing se dešava kada forma treba da se zatvori: klikom na X, tasterima Alt+F4 ili naredbom this.Close(). Ako u njemu postavimo e.Cancel = true, forma ostaje otvorena. Ovo koristimo za pitanje „Da li zaista želite da izađete?“.

## Čas 1 – Izrada: prva verzija Konvertora

### Korak 1. Novi projekat

U Visual C# 2010 Express izaberite File → New Project → Windows Forms Application. Projekat nazovite KonvertorJedinica.

### Korak 2. Izgled forme

Ostavite oko 30 piksela slobodno na vrhu forme, tu ćemo na času 2 dodati meni. Ispod toga stoji labela sa nazivom izabrane konverzije. U sledećem redu su labela „Vrednost:“, polje za unos i, desno od njega, dugme Konvertuj. Na dnu, krupnim podebljanim slovima, ispisuje se rezultat.

| Kontrola | Name | Svojstva u prozoru Properties |
| --- | --- | --- |
| Form | Form1 | Text: Konvertor jedinica; StartPosition: CenterScreen; FormBorderStyle: FixedSingle; MaximizeBox: False |
| Label | lblKonverzija | Text: Konverzija: Kilometri u milje |
| Label | lblUnos | Text: Vrednost: |
| TextBox | txtUnos | (podrazumevano) |
| Button | btnKonvertuj | Text: &Konvertuj |
| Label | lblRezultat | Text: (obrisati); Font: Size 16, Bold |

Znak & ispred slova K znači da dugme možete pritisnuti i tasterima Alt+K.

### Korak 3. Dugme Konvertuj

Dvoklik na dugme otvara rukovalac btnKonvertuj\_Click. Na ovom času pretvaramo samo kilometre u milje.

```csharp
private void btnKonvertuj_Click(object sender, EventArgs e)
{
    double vrednost;
    if (!double.TryParse(txtUnos.Text, out vrednost))
    {
        MessageBox.Show("Unesite broj, na primer 12,5.", "Greška pri unosu",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
        txtUnos.Focus();
        txtUnos.SelectAll();
        return;
    }

    if (vrednost < 0)
    {
        MessageBox.Show("Za ovu konverziju vrednost ne može biti negativna.", "Upozorenje",
            MessageBoxButtons.OK, MessageBoxIcon.Warning);
        txtUnos.Focus();
        txtUnos.SelectAll();
        return;
    }

    double rezultat = vrednost * 0.621371;
    lblRezultat.Text = rezultat + " mi";
}
```

Ključni redovi:

- **double.TryParse(txtUnos.Text, out vrednost)** pokušava da pretvori tekst u broj. Ako uspe, vraća true i broj upisuje u promenljivu vrednost. Ako ne uspe (npr. upisano je „abc“), vraća false i program se ne ruši, za razliku od Convert.ToDouble.
- **Error** koristimo kada je korisnik napravio grešku, a **Warning** kada je unos ispravan broj, ali nema smisla.
- **Focus() i SelectAll()** vraćaju kursor u polje i označe pogrešan unos, pa korisnik odmah kuca novi broj.
- **return** prekida metodu, pa se konverzija ne izvršava.

Pažnja: decimalni broj upišite onako kako je podešeno u Windowsu. Uz srpska podešavanja to je zarez (12,5), a uz engleska tačka (12.5).

### Korak 4. Pitanje pri izlasku

Izaberite formu (klik na naslovnu traku forme u dizajneru). U prozoru Properties kliknite na ikonicu munje (kartica Events), pronađite FormClosing i uradite dvoklik.

```csharp
private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    DialogResult odgovor = MessageBox.Show("Da li zaista želite da izađete?", "Izlaz",
        MessageBoxButtons.YesNo, MessageBoxIcon.Question);

    if (odgovor == DialogResult.No)
    {
        e.Cancel = true;   // otkazuje zatvaranje forme
    }
}
```

**Proverite:** upišite „abc“, zatim „-5“, zatim „10“ i kliknite Konvertuj. Na kraju zatvorite formu na X i probajte oba odgovora.

## Čas 2 – Glavni meni

Glavni meni (MenuStrip) je traka na vrhu prozora sa grupama komandi, npr. Datoteka, Uređivanje, Pomoć. Uvek je vidljiv i sadrži sve važne komande programa. Otvorite Notepad i pritisnite Alt: videćete podvučena slova u meniju.

**Delovi menija**

| Deo | Klasa | Primer u Konvertoru |
| --- | --- | --- |
| traka menija | MenuStrip | traka na vrhu forme |
| stavka menija | ToolStripMenuItem | Datoteka, Izlaz, Kilometri u milje |
| podmeni | stavke ispod stavke | sve stavke ispod Konverzija |
| separator (crta između grupa) | ToolStripSeparator | crta iznad Izlaz |

**Važna svojstva stavke menija**

| Svojstvo | Šta radi | Primer |
| --- | --- | --- |
| Name | ime stavke u kodu | mnuIzlaz |
| Text | natpis; & ispred slova pravi pristupni taster | &Izlaz (prikazuje se Izlaz, slovo I podvučeno) |
| ShortcutKeys | prečica koja radi i kada je meni zatvoren | Ctrl+Q |
| Checked | stavka je štiklirana (ima kvačicu) | izabrana konverzija |
| CheckOnClick | stavka se sama štiklira i odštiklira na klik | Zaokruži na 2 decimale |
| Enabled | False: stavka je siva i ne može da se klikne | Kopiraj rezultat dok nema rezultata |

**Pristupni taster i prečica nisu isto.** Pristupni taster (&) radi kroz meni: Alt+D otvara Datoteka, zatim I bira Izlaz. Prečica (ShortcutKeys) odmah izvršava komandu, bez otvaranja menija: Ctrl+Q.

**Checked i CheckOnClick.** Kada korisnik bira jednu od više mogućnosti (koja konverzija), štikliranje radi naš kod: skinemo kvačicu sa svih, pa je stavimo na izabranu. Kada je opcija samostalna, uključeno ili isključeno (zaokruživanje), postavimo CheckOnClick na True i stavka se štiklira sama.

**Kako se pravi meni**

1. U kutiji sa alatima (Toolbox), grupa Menus & Toolbars, prevucite MenuStrip na formu. Traka se pojavi na vrhu forme, a ikonica menuStrip1 u traci ispod forme.
2. Kliknite na polje „Type Here“ i upišite natpis stavke. Desno se dodaje sledeći meni, a ispod stavka podmenija.
3. Za separator upišite samo znak - (minus).
4. Izaberite stavku i u prozoru Properties prvo promenite Name, pa tek onda uradite dvoklik da dobijete rukovalac Click.

## Čas 2 – Izrada: meniji Konvertora

### Korak 5. Pravljenje menija

Dodajte MenuStrip i napravite tri menija sa stavkama iz tabele. Uvučene stavke idu u podmeni.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| MenuStrip | menuStrip1 | (podrazumevano) |
| meni | mnuDatoteka | Text: &Datoteka |
| stavka | mnuNovo | Text: &Novo konvertovanje; ShortcutKeys: Ctrl+N |
| separator | toolStripSeparator1 | (upisati „-“) |
| stavka | mnuIzlaz | Text: \&Izlaz; ShortcutKeys: Ctrl+Q |
| meni | mnuKonverzija | Text: &Konverzija |
| stavka | mnuKmUMilje | Text: &Kilometri u milje; ShortcutKeys: Ctrl+1 |
| stavka | mnuCelzijusUFarenhajte | Text: &Celzijusi u Farenhajte; ShortcutKeys: Ctrl+2 |
| stavka | mnuKgUFunte | Text: Ki&logrami u funte; ShortcutKeys: Ctrl+3 |
| stavka | mnuInciUCm | Text: &Inči u centimetre; ShortcutKeys: Ctrl+4 |
| separator | toolStripSeparator2 | (upisati „-“) |
| stavka | mnuZaokruzi | Text: &Zaokruži na 2 decimale; CheckOnClick: True; Checked: True |
| meni | mnuPomoc | Text: &Pomoć |
| stavka | mnuOProgramu | Text: &O programu; ShortcutKeys: F1 |

U meniju Konverzija i Kilometri i Kilogrami počinju slovom K. Zato je kod Kilograma podvučeno slovo L (Ki&logrami): u jednom meniju svako podvučeno slovo sme da se pojavi samo jednom.

### Korak 6. Izbor konverzije

Četiri stavke konverzije rade isto: skinu kvačicu sa svih, stave je na izabranu i ispišu naziv. Zato taj posao pišemo jednom, u pomoćnoj metodi, koju pozivaju sva četiri rukovaoca.

```csharp
// Štiklira samo izabranu stavku i ispisuje naziv konverzije
private void IzaberiKonverziju(ToolStripMenuItem stavka)
{
    mnuKmUMilje.Checked = false;
    mnuCelzijusUFarenhajte.Checked = false;
    mnuKgUFunte.Checked = false;
    mnuInciUCm.Checked = false;
    stavka.Checked = true;

    // Iz teksta stavke uklanjamo znak & (pristupni taster)
    lblKonverzija.Text = "Konverzija: " + stavka.Text.Replace("&", "");
    lblRezultat.Text = "";
}

private void Form1_Load(object sender, EventArgs e)
{
    // Na početku je izabrana prva konverzija
    IzaberiKonverziju(mnuKmUMilje);
}

private void mnuKmUMilje_Click(object sender, EventArgs e)
{
    IzaberiKonverziju(mnuKmUMilje);
}

private void mnuCelzijusUFarenhajte_Click(object sender, EventArgs e)
{
    IzaberiKonverziju(mnuCelzijusUFarenhajte);
}

private void mnuKgUFunte_Click(object sender, EventArgs e)
{
    IzaberiKonverziju(mnuKgUFunte);
}

private void mnuInciUCm_Click(object sender, EventArgs e)
{
    IzaberiKonverziju(mnuInciUCm);
}
```

Rukovalac Form1\_Load dobijate dvoklikom na prazan deo forme.

### Korak 7. Nova verzija dugmeta Konvertuj

Sada dugme proverava koja je stavka štiklirana i prema njoj bira formulu. Zamenite ceo rukovalac btnKonvertuj\_Click:

```csharp
private void btnKonvertuj_Click(object sender, EventArgs e)
{
    double vrednost;
    if (!double.TryParse(txtUnos.Text, out vrednost))
    {
        MessageBox.Show("Unesite broj, na primer 12,5.", "Greška pri unosu",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
        txtUnos.Focus();
        txtUnos.SelectAll();
        return;
    }

    // Negativna vrednost ima smisla samo za temperaturu
    if (vrednost < 0 && !mnuCelzijusUFarenhajte.Checked)
    {
        MessageBox.Show("Za ovu konverziju vrednost ne može biti negativna.", "Upozorenje",
            MessageBoxButtons.OK, MessageBoxIcon.Warning);
        txtUnos.Focus();
        txtUnos.SelectAll();
        return;
    }

    double rezultat;
    string jedinica;

    if (mnuKmUMilje.Checked)
    {
        rezultat = vrednost * 0.621371;
        jedinica = "mi";
    }
    else if (mnuCelzijusUFarenhajte.Checked)
    {
        rezultat = vrednost * 9 / 5 + 32;
        jedinica = "°F";
    }
    else if (mnuKgUFunte.Checked)
    {
        rezultat = vrednost * 2.20462;
        jedinica = "lb";
    }
    else
    {
        rezultat = vrednost * 2.54;
        jedinica = "cm";
    }

    if (mnuZaokruzi.Checked)
    {
        rezultat = Math.Round(rezultat, 2);
    }

    lblRezultat.Text = rezultat + " " + jedinica;
}
```

- **mnuKmUMilje.Checked** je true ako je stavka štiklirana. Pošto je uvek štiklirana tačno jedna, if – else if – else bira tačno jednu formulu.
- **Math.Round(rezultat, 2)** zaokružuje na dve decimale, ali samo ako je uključena opcija Zaokruži.

### Korak 8. Datoteka i Pomoć

```csharp
private void mnuNovo_Click(object sender, EventArgs e)
{
    txtUnos.Clear();
    lblRezultat.Text = "";
    txtUnos.Focus();
}

private void mnuIzlaz_Click(object sender, EventArgs e)
{
    this.Close();   // pokreće događaj FormClosing
}

private void mnuOProgramu_Click(object sender, EventArgs e)
{
    MessageBox.Show("Konvertor jedinica, verzija 1.0\n" +
        "Pretvara dužinu, temperaturu, masu i inče.\n\n" +
        "Autor: Marko Marković, IV-2",
        "O programu", MessageBoxButtons.OK, MessageBoxIcon.Information);
}
```

Izlaz ne pita ništa, samo zatvara formu. Pitanje već postoji u FormClosing, a on se poziva i za Izlaz i za X. Ako biste pitanje napisali i ovde, pojavilo bi se dva puta. U poruci „O programu“ upišite svoje ime i odeljenje.

**Proverite:** Ctrl+1 do Ctrl+4 menjaju konverziju i kvačicu; Alt+K otvara meni Konverzija; F1 otvara „O programu“; Ctrl+Q traži potvrdu izlaska.

## Čas 3 – Pomoćni meni

Pomoćni (kontekstni) meni (ContextMenuStrip) otvara se desnim klikom i nudi samo komande za ono na šta je kliknuto. Desni klik na datoteku nudi Otvori i Preimenuj, a desni klik na tekst u Wordu nudi Iseci, Kopiraj i Nalepi. Zato se zove kontekstni: sadržaj zavisi od mesta klika.

**Kako se pravi pomoćni meni**

1. Iz grupe Menus & Toolbars prevucite ContextMenuStrip na formu. On se ne vidi na formi, već samo kao ikonica u traci ispod forme.
2. Kliknite na tu ikonicu: na vrhu forme pojaviće se polje „Type Here“ i stavke upisujete isto kao u glavnom meniju.
3. Pomoćni meni povežite sa kontrolom: izaberite kontrolu i u njenom svojstvu **ContextMenuStrip** izaberite ime pomoćnog menija. Bez ovog koraka desni klik ne otvara vaš meni.

Polje za unos (TextBox) već ima svoj ugrađeni pomoćni meni (Undo, Cut, Copy, Paste). Kada mu dodelite svoj, on zamenjuje ugrađeni.

Jedan pomoćni meni može se dodeliti i formi, pa se otvara desnim klikom bilo gde na praznom delu forme. Forma može imati više pomoćnih menija, za svaku kontrolu drugi.

**Događaj Opening.** Pomoćni meni ima događaj Opening koji se dešava neposredno pre nego što se meni prikaže. U njemu možemo da isključimo (Enabled = false) stavke koje trenutno nemaju smisla. Biće vam potreban u Zadatku 7.

**Ostava (Clipboard).** Naredba Clipboard.SetText(tekst) kopira tekst u ostavu, isto kao Ctrl+C. Posle toga tekst možete nalepiti bilo gde sa Ctrl+V. Ako je tekst prazan, nastaje greška, zato stavku Kopiraj rezultat isključujemo dok nema rezultata.

### Glavni ili pomoćni meni? (rad u paru)

Sa drugom iz klupe popunite tabelu. Proverićemo je zajedno na kraju.

| Pitanje | Glavni meni | Pomoćni meni |
| --- | --- | --- |
| Gde se nalazi na ekranu? |  |  |
| Da li je stalno vidljiv? |  |  |
| Kako se otvara? |  |  |
| Na šta se odnose njegove komande? |  |  |
| Koliko ih forma može imati? |  |  |
| Kako se klasa zove u Visual Studiju? |  |  |
| Kako se povezuje sa kontrolom? |  |  |
| Primer komande iz Konvertora |  |  |

U koji meni biste stavili ove komande: Snimi, Obriši ovu stavku, O programu, Kopiraj ovu sliku? Obrazložite.

## Čas 3 – Izrada: pomoćni meni Konvertora

### Korak 9. Pomoćni meni

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| ContextMenuStrip | cmsUnos | (podrazumevano) |
| stavka pomoćnog menija | mnuObrisiUnos | Text: &Obriši unos |
| stavka pomoćnog menija | mnuKopirajRezultat | Text: &Kopiraj rezultat; Enabled: False |

### Korak 10. Povezivanje sa poljem za unos

Izaberite txtUnos i u svojstvu ContextMenuStrip izaberite cmsUnos.

### Korak 11. Kod pomoćnog menija

Napišite dva nova rukovaoca:

```csharp
private void mnuObrisiUnos_Click(object sender, EventArgs e)
{
    txtUnos.Clear();
    txtUnos.Focus();
}

private void mnuKopirajRezultat_Click(object sender, EventArgs e)
{
    Clipboard.SetText(lblRezultat.Text);
}
```

Stavka Kopiraj rezultat treba da bude uključena samo kada rezultat postoji. Dopunite postojeći kod na tri mesta:

| Gde | Dodati red |
| --- | --- |
| na kraj btnKonvertuj\_Click (rezultat je upravo ispisan) | mnuKopirajRezultat.Enabled = true; |
| na kraj IzaberiKonverziju (rezultat je obrisan) | mnuKopirajRezultat.Enabled = false; |
| u mnuNovo\_Click, posle lblRezultat.Text = ""; | mnuKopirajRezultat.Enabled = false; |

**Proverite:** desni klik na polje pre konverzije (Kopiraj rezultat je siv); posle konverzije kopirajte rezultat i nalepite ga u Notepad sa Ctrl+V.

### Ceo kod Form1.cs

Ovako izgleda gotova aplikacija. Uporedite sa svojim kodom.

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace KonvertorJedinica
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            // Na početku je izabrana prva konverzija
            IzaberiKonverziju(mnuKmUMilje);
        }

        // Štiklira samo izabranu stavku i ispisuje naziv konverzije
        private void IzaberiKonverziju(ToolStripMenuItem stavka)
        {
            mnuKmUMilje.Checked = false;
            mnuCelzijusUFarenhajte.Checked = false;
            mnuKgUFunte.Checked = false;
            mnuInciUCm.Checked = false;
            stavka.Checked = true;

            // Iz teksta stavke uklanjamo znak & (pristupni taster)
            lblKonverzija.Text = "Konverzija: " + stavka.Text.Replace("&", "");
            lblRezultat.Text = "";
            mnuKopirajRezultat.Enabled = false;
        }

        private void btnKonvertuj_Click(object sender, EventArgs e)
        {
            double vrednost;
            if (!double.TryParse(txtUnos.Text, out vrednost))
            {
                MessageBox.Show("Unesite broj, na primer 12,5.", "Greška pri unosu",
                    MessageBoxButtons.OK, MessageBoxIcon.Error);
                txtUnos.Focus();
                txtUnos.SelectAll();
                return;
            }

            // Negativna vrednost ima smisla samo za temperaturu
            if (vrednost < 0 && !mnuCelzijusUFarenhajte.Checked)
            {
                MessageBox.Show("Za ovu konverziju vrednost ne može biti negativna.", "Upozorenje",
                    MessageBoxButtons.OK, MessageBoxIcon.Warning);
                txtUnos.Focus();
                txtUnos.SelectAll();
                return;
            }

            double rezultat;
            string jedinica;

            if (mnuKmUMilje.Checked)
            {
                rezultat = vrednost * 0.621371;
                jedinica = "mi";
            }
            else if (mnuCelzijusUFarenhajte.Checked)
            {
                rezultat = vrednost * 9 / 5 + 32;
                jedinica = "°F";
            }
            else if (mnuKgUFunte.Checked)
            {
                rezultat = vrednost * 2.20462;
                jedinica = "lb";
            }
            else
            {
                rezultat = vrednost * 2.54;
                jedinica = "cm";
            }

            if (mnuZaokruzi.Checked)
            {
                rezultat = Math.Round(rezultat, 2);
            }

            lblRezultat.Text = rezultat + " " + jedinica;
            mnuKopirajRezultat.Enabled = true;
        }

        // ---------- Glavni meni: Datoteka ----------

        private void mnuNovo_Click(object sender, EventArgs e)
        {
            txtUnos.Clear();
            lblRezultat.Text = "";
            mnuKopirajRezultat.Enabled = false;
            txtUnos.Focus();
        }

        private void mnuIzlaz_Click(object sender, EventArgs e)
        {
            this.Close();   // pokreće događaj FormClosing
        }

        // ---------- Glavni meni: Konverzija ----------

        private void mnuKmUMilje_Click(object sender, EventArgs e)
        {
            IzaberiKonverziju(mnuKmUMilje);
        }

        private void mnuCelzijusUFarenhajte_Click(object sender, EventArgs e)
        {
            IzaberiKonverziju(mnuCelzijusUFarenhajte);
        }

        private void mnuKgUFunte_Click(object sender, EventArgs e)
        {
            IzaberiKonverziju(mnuKgUFunte);
        }

        private void mnuInciUCm_Click(object sender, EventArgs e)
        {
            IzaberiKonverziju(mnuInciUCm);
        }

        // ---------- Glavni meni: Pomoć ----------

        private void mnuOProgramu_Click(object sender, EventArgs e)
        {
            MessageBox.Show("Konvertor jedinica, verzija 1.0\n" +
                "Pretvara dužinu, temperaturu, masu i inče.\n\n" +
                "Autor: Marko Marković, IV-2",
                "O programu", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }

        // ---------- Pomoćni (kontekstni) meni na polju za unos ----------

        private void mnuObrisiUnos_Click(object sender, EventArgs e)
        {
            txtUnos.Clear();
            txtUnos.Focus();
        }

        private void mnuKopirajRezultat_Click(object sender, EventArgs e)
        {
            Clipboard.SetText(lblRezultat.Text);
        }

        // ---------- Potvrda izlaska ----------

        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {
            DialogResult odgovor = MessageBox.Show("Da li zaista želite da izađete?", "Izlaz",
                MessageBoxButtons.YesNo, MessageBoxIcon.Question);

            if (odgovor == DialogResult.No)
            {
                e.Cancel = true;   // otkazuje zatvaranje forme
            }
        }
    }
}
```

## Rezime

- **MessageBox.Show(tekst, naslov, dugmad, ikonica)** prikazuje poruku. Ikonicu biramo prema vrsti poruke: Information, Warning, Error, Question.
- Odgovor korisnika čuvamo u promenljivoj tipa **DialogResult** i proveravamo sa if. U događaju **FormClosing** naredba e.Cancel = true sprečava zatvaranje forme.
- **Glavni meni (MenuStrip)** je stalno vidljiv na vrhu forme i sadrži sve komande programa. Stavke imaju pristupni taster (&), prečicu (ShortcutKeys) i stanja Checked i Enabled.
- **Pomoćni meni (ContextMenuStrip)** se otvara desnim klikom i sadrži komande samo za kontrolu na koju je kliknuto. Sa kontrolom se povezuje njenim svojstvom ContextMenuStrip.

## Pitanja za samoproveru

1. Koja dugmad i koju ikonicu biste izabrali za poruku „Da li želite da obrišete sve rezultate?“ Zašto?
2. Šta vraća metoda MessageBox.Show i kako proveravate da li je korisnik kliknuo No?
3. Po čemu se razlikuju pristupni taster (&) i prečica (ShortcutKeys)?
4. Kada stavci postavljate CheckOnClick na True, a kada štikliranje radite u kodu?
5. Napravili ste pomoćni meni, ali se desnim klikom na polje i dalje otvara ugrađeni meni (Undo, Cut, Copy…). Šta ste zaboravili?

## Zadaci

Zadaci su označeni nivoom: \[OSNOVNI\], \[SREDNJI\] i \[NAPREDNI\]. Svaki zadatak je novi projekat, osim zadataka 8 i 9 koji nadograđuju Konvertor (pre izmena napravite kopiju projekta).

| Zadatak | Nivo | Kada |
| --- | --- | --- |
| 1. Poruke | \[OSNOVNI\] | na času 1 |
| 2. Anketa | \[OSNOVNI\] | domaći posle časa 1 |
| 3. Brojač poena | \[SREDNJI\] | domaći posle časa 1 |
| 4. Boje forme | \[OSNOVNI\] | na času 2 ko završi Konvertor, ostali za domaći |
| 5. Brojač sa menijem | \[SREDNJI\] | domaći posle časa 2 |
| 6. Tabla | \[OSNOVNI\] | na času 3 |
| 7. Lista kupovine | \[SREDNJI\] | na času 3 ko stigne, ostali za domaći |
| 8. Konvertor: obrnuti smer | \[NAPREDNI\] | domaći posle časa 3 |
| 9. Konvertor: istorija konverzija | \[NAPREDNI\] | domaći posle časa 3 |

### Zadatak 1 \[OSNOVNI\] – Poruke

Napravite formu sa tri dugmeta: Informacija, Upozorenje i Greška. Svako dugme prikazuje poruku sa odgovarajućim naslovom i ikonicom. Tekstove smislite sami, iz školskog života (npr. „Čas počinje u 8:00.“).

### Zadatak 2 \[OSNOVNI\] – Anketa

Dugme Glasaj prikazuje pitanje „Da li ti se dopada programiranje?“ sa dugmadima Yes i No. Prema odgovoru, u jednoj labeli ispišite poruku (npr. „Odlično, i nama!“), a u drugoj ukupan broj glasova: „Da: 3   Ne: 1“.

### Zadatak 3 \[SREDNJI\] – Brojač poena

Dugme „+1 poen“ povećava broj poena koji se ispisuje u labeli. Dugme „Nova igra“ pita da li da se sačuva rezultat, sa dugmadima Yes, No i Cancel:

- Yes: u listu upisuje „Igra 1: 7 poena“ i počinje novu igru;
- No: počinje novu igru bez upisa;
- Cancel: ništa se ne menja, igra se nastavlja.

### Zadatak 4 \[OSNOVNI\] – Boje forme

Napravite meni Boja sa stavkama Crvena (Ctrl+1), Zelena (Ctrl+2), Plava (Ctrl+3), separatorom i stavkom Podrazumevana (Ctrl+0), i meni Pomoć sa stavkom O programu (F1). Izabrana boja menja pozadinu forme (koristite svetle nijanse, npr. Color.LightBlue) i samo ona je štiklirana. Na početku je štiklirana Podrazumevana. Savet: napravite pomoćnu metodu kao IzaberiKonverziju.

### Zadatak 5 \[SREDNJI\] – Brojač sa menijem

Na sredini forme je veliki broj, na početku 0. Meni Brojač ima stavke Povećaj (Ctrl+P), Smanji (Ctrl+M), separator i Resetuj (Ctrl+R). Meni Opcije ima stavku Dozvoli negativne brojeve koja se uključuje i isključuje klikom.

- Kada negativni brojevi nisu dozvoljeni, a broj je 0, stavka Smanji je siva.
- Ako korisnik isključi opciju dok je broj negativan, prikažite poruku i vratite broj na 0.

Savet: za opciju koristite događaj CheckedChanged (kartica Events).

### Zadatak 6 \[OSNOVNI\] – Tabla

Desnim klikom bilo gde na formi otvara se pomoćni meni sa stavkama Bela, Žuta, Svetloplava, separatorom i stavkom Zatvori. Boje menjaju pozadinu forme, a Zatvori zatvara formu. Savet: pomoćni meni dodelite samoj formi.

### Zadatak 7 \[SREDNJI\] – Lista kupovine

Polje za unos i dugme Dodaj dodaju namirnicu u listu. Ako je polje prazno, prikažite upozorenje. Na listi napravite pomoćni meni sa stavkama:

- Obriši izabranu stavku – siva ako ništa nije izabrano;
- Obriši sve – siva ako je lista prazna; pre brisanja traži potvrdu (Yes/No).

Savet: stavke uključujte i isključujte u događaju Opening pomoćnog menija.

### Zadatak 8 \[NAPREDNI\] – Konvertor: obrnuti smer

Na dno menija Konverzija dodajte stavku Obrnuti smer (Ctrl+R) koja se uključuje i isključuje klikom. Kada je uključena, Konvertor računa obrnuto: milje u kilometre, °F u °C, funte u kilograme, centimetre u inče. Labela sa nazivom konverzije tada na kraju ima „(obrnuti smer)“.

### Zadatak 9 \[NAPREDNI\] – Konvertor: istorija konverzija

Dodajte listu u koju se posle svake uspešne konverzije upisuje red, npr. „12,5 → 7,77 mi“. Na listi napravite pomoćni meni sa stavkama Vrati u polje za unos (uneta vrednost iz izabranog reda vraća se u polje) i Obriši istoriju. U meni Datoteka dodajte stavku Prikaži istoriju koja se uključuje i isključuje klikom i sakriva ili prikazuje listu.
