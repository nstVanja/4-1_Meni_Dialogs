## Materijal za učenike: Evidencija učenika

Oct 18, 2026 · @Vanja

## Šta pravimo

Za tri časa napravićete „Evidenciju učenika“: program sa pet formi koje sarađuju. Glavna forma ima listu učenika. Nov učenik se upisuje u posebnoj formi za unos, a podaci o izabranom učeniku otvaraju se u posebnom prozoru.

| Čas | Šta učimo | Koje forme dodajemo |
| --- | --- | --- |
| 10 | dodavanje formi; modalni i nemodalni prozor | frmOProgramu (modalna), frmUputstvo (nemodalna) |
| 11 | forma za unos koja vraća podatke | frmUnos (modalna) |
| 12 | slanje podataka u formu pri otvaranju | frmDetalji (nemodalna) |

**Imenovanje.** Nove forme imaju prefiks frm. Ime forme se zadaje odmah pri dodavanju, jer je to ujedno ime datoteke i ime klase. Glavna forma ostaje Form1.

**Kako čuvamo učenike.** Svaki učenik je u listi jedan red teksta u obliku „Prezime Ime, Odeljenje“, npr. Petrović Marko, IV-2.

## Čas 10 – Rad sa više formi

**Nova forma.** Formu dodajete izborom Project → Add Windows Form; u polje Name upišete ime, npr. frmOProgramu.cs. Nova forma ima svoj dizajner i svoj kod, kao Form1.

**Forma je klasa, prozor je objekat.** Ono što nacrtate u dizajneru je opis prozora (klasa). Da bi se prozor pojavio na ekranu, u kodu pravite objekat te klase rečju new i prikazujete ga:

```csharp
frmOProgramu oProgramu = new frmOProgramu();   // pravi prozor
oProgramu.ShowDialog();                          // prikazuje ga
```

Svaki new pravi nov, poseban prozor. Zato istu formu možete otvoriti više puta, sa različitim sadržajem.

**Modalni i nemodalni prozor**

|  | ShowDialog() – modalni | Show() – nemodalni |
| --- | --- | --- |
| Ostatak programa | ne može se koristiti dok se prozor ne zatvori | može se koristiti, oba prozora rade |
| Kod posle poziva | čeka da se prozor zatvori | izvršava se odmah |
| Vraća | DialogResult (koje je dugme zatvorilo prozor) | ništa |
| Više kopija istog prozora | ne može | može |
| Primer u Wordu | prozor Font (Ctrl+D) | Find and Replace (Ctrl+H) |
| Primer u Evidenciji | O programu, forma za unos | Uputstvo, detalji o učeniku |

Modalni prozor koristite kada program ne sme da nastavi bez odgovora korisnika. Nemodalni koristite kada korisnik treba da radi i u glavnom prozoru dok je drugi otvoren.

**Zatvaranje.** Forma sama sebe zatvara naredbom this.Close();.

**Svojstva forme koja služi kao dijalog**

| Svojstvo | Vrednost | Zašto |
| --- | --- | --- |
| FormBorderStyle | FixedDialog | ivica se ne može razvlačiti |
| MaximizeBox, MinimizeBox | False | dijalog se ne uvećava i ne umanjuje |
| ShowInTaskbar | False | ne dobija svoje mesto u traci zadataka |
| StartPosition | CenterParent | otvara se na sredini prozora koji ga je otvorio |

Ovaj skup svojstava u daljem tekstu zovemo kraće: **dijalog-forma**.

## Čas 10 – Izrada: glavna forma i prva dva prozora

### Korak 1. Novi projekat i glavna forma

Napravite projekat Windows Forms Application pod imenom EvidencijaUcenika. Na glavnu formu postavite sve kontrole odmah, iako će neka dugmad proraditi tek na času 11 i 12.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| Form | Form1 | Text: Evidencija učenika; Size: 520; 400; StartPosition: CenterScreen |
| Label | lblNaslov | Text: Učenici: |
| ListBox | lstUcenici | Size: 300; 250 |
| Label | lblBroj | Text: Ukupno učenika: 0 |
| Button | btnDodaj | Text: &Dodaj... (čas 11) |
| Button | btnDetalji | Text: D&etalji (čas 12) |
| Button | btnUputstvo | Text: &Uputstvo |
| Button | btnOProgramu | Text: &O programu |

Dodaj ima tri tačke jer otvara prozor u kome korisnik još nešto upisuje.

### Korak 2. Forma O programu (modalna)

Project → Add Windows Form, Name: frmOProgramu.cs.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| Form | frmOProgramu | Text: O programu; Size: 320; 180; dijalog-forma |
| Label | lblNaziv | Text: Evidencija učenika, verzija 1.0; Font: Bold |
| Label | lblAutor | Text: Autor: Marko Marković, IV-2 |
| Button | btnZatvori | Text: Zatvori |

Dvoklik na btnZatvori:

```csharp
private void btnZatvori_Click(object sender, EventArgs e)
{
    this.Close();
}
```

### Korak 3. Forma Uputstvo (nemodalna)

Project → Add Windows Form, Name: frmUputstvo.cs.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| Form | frmUputstvo | Text: Uputstvo; Size: 300; 260; FormBorderStyle: SizableToolWindow |
| TextBox | txtUputstvo | Multiline: True; ReadOnly: True; Dock: Fill; ScrollBars: Vertical |

U svojstvo Lines polja txtUputstvo upišite (svaku rečenicu u svom redu): Dodaj... – upis novog učenika. / Detalji – podaci o izabranom učeniku. / Dvoklik na učenika takođe otvara detalje. Ova forma nema kod.

### Korak 4. Otvaranje iz glavne forme

U Form1 dvoklikom na dugmad napravite:

```csharp
private void btnUputstvo_Click(object sender, EventArgs e)
{
    frmUputstvo uputstvo = new frmUputstvo();
    uputstvo.Show();          // nemodalno: može se raditi i u glavnoj formi
}

private void btnOProgramu_Click(object sender, EventArgs e)
{
    frmOProgramu oProgramu = new frmOProgramu();
    oProgramu.ShowDialog();   // modalno: glavna forma čeka da se ovaj prozor zatvori
}
```

### Korak 5. Uporedite dva prozora

- Kliknite tri puta na Uputstvo: otvaraju se tri prozora, a u glavnoj formi i dalje možete da klikćete.
- Kliknite na O programu i pokušajte da kliknete na glavnu formu: ne reaguje dok ne zatvorite O programu.
- Pogledajte traku zadataka: O programu nema svoje dugme.
- Probno, ispod oProgramu.ShowDialog(); dodajte MessageBox.Show("Prozor je zatvoren."); Poruka se pojavljuje tek kada zatvorite O programu. Uradite isto ispod uputstvo.Show(); i uporedite. Na kraju obrišite oba probna reda.

## Čas 11 – Forma za unos koja vraća podatke

Forma za unos radi kao MessageBox koji ste sami napravili: prikazuje se modalno, korisnik nešto upiše, klikne U redu ili Odustani, i glavna forma dobija odgovor. Treba nam odgovor na dva pitanja: da li je korisnik potvrdio unos i šta je upisao.

**1. Da li je potvrdio: DialogResult.** Svaka forma ima svojstvo DialogResult. Kada mu modalna forma dodeli vrednost, ona se zatvara, a ShowDialog() vraća tu vrednost glavnoj formi.

- Dugmetu Odustani u Properties postavite DialogResult = Cancel. Klik na njega zatvara formu bez ijednog reda koda.
- Dugmetu U redu DialogResult ne postavljate u Properties. U njegovoj metodi za obradu događaja prvo proverite da li su polja popunjena, pa tek onda napišite this.DialogResult = DialogResult.OK;. Tako forma ostaje otvorena dok podaci nisu ispravni.

**2. Enter i Esc: AcceptButton i CancelButton.** To su svojstva forme. AcceptButton je dugme koje se „pritisne“ tasterom Enter (U redu), a CancelButton dugme za taster Esc (Odustani). Isto rade svi dijalozi u Windowsu.

**3. Šta je upisao: javno svojstvo.** Polja za unos (txtIme i ostala) su privatna: glavna forma ne može da napiše unos.txtIme.Text. Zato forma za unos otvara „javni prozorčić“, svojstvo, kroz koje drugi mogu samo da čitaju:

```csharp
public string Ime
{
    get { return txtIme.Text.Trim(); }
}
```

Svojstvo izgleda kao promenljiva (unos.Ime), ali svaki put kada ga neko pročita izvrši se kod u delu get. Reč public znači da je dostupno i drugim formama.

**4. Glavna forma sve to koristi ovako:**

```csharp
frmUnos unos = new frmUnos();
if (unos.ShowDialog() == DialogResult.OK)
{
    // korisnik je potvrdio, pa čitamo svojstva
    lstUcenici.Items.Add(unos.Prezime + " " + unos.Ime + ", " + unos.Odeljenje);
}
```

Forma je zatvorena, ali objekat unos i dalje postoji, pa se svojstva mogu čitati i posle zatvaranja.

## Čas 11 – Izrada: forma za unos

### Korak 6. Forma za unos

Project → Add Windows Form, Name: frmUnos.cs. Prvo postavite kontrole, pa tek onda svojstva forme AcceptButton i CancelButton (u njihovoj padajućoj listi se vide samo dugmad koja već postoje).

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| Form | frmUnos | Text: Novi učenik; Size: 320; 220; dijalog-forma; AcceptButton: btnUredu; CancelButton: btnOdustani |
| Label i TextBox | lblIme, txtIme | lblIme.Text: Ime: |
| Label i TextBox | lblPrezime, txtPrezime | lblPrezime.Text: Prezime: |
| Label i TextBox | lblOdeljenje, txtOdeljenje | lblOdeljenje.Text: Odeljenje: |
| Button | btnUredu | Text: U redu (DialogResult ostaje None) |
| Button | btnOdustani | Text: Odustani; DialogResult: Cancel |

### Korak 7. Kod forme za unos

Otvorite kod frmUnos (F7) i u klasu, ispod konstruktora, dodajte tri svojstva, a dvoklikom na btnUredu u dizajneru napravite njegovu metodu za obradu događaja:

```csharp
// Javna svojstva: glavna forma preko njih čita unete podatke
public string Ime
{
    get { return txtIme.Text.Trim(); }
}

public string Prezime
{
    get { return txtPrezime.Text.Trim(); }
}

public string Odeljenje
{
    get { return txtOdeljenje.Text.Trim(); }
}

private void btnUredu_Click(object sender, EventArgs e)
{
    if (Ime == "" || Prezime == "" || Odeljenje == "")
    {
        MessageBox.Show("Popunite sva polja.", "Novi učenik",
            MessageBoxButtons.OK, MessageBoxIcon.Warning);
        return;   // forma ostaje otvorena
    }
    this.DialogResult = DialogResult.OK;   // zatvara formu; ShowDialog vraća OK
}
```

Forma koristi sopstvena svojstva (Ime, Prezime, Odeljenje) i za proveru. Dugme Odustani nema kod: zatvara formu zbog DialogResult = Cancel.

### Korak 8. Glavna forma dodaje učenika

U Form1 napravite metode za obradu događaja Form1\_Load (dvoklik na formu) i btnDodaj\_Click i dodajte metodu OsveziBroj:

```csharp
private void Form1_Load(object sender, EventArgs e)
{
    OsveziBroj();
}

private void OsveziBroj()
{
    lblBroj.Text = "Ukupno učenika: " + lstUcenici.Items.Count;
}

private void btnDodaj_Click(object sender, EventArgs e)
{
    frmUnos unos = new frmUnos();
    if (unos.ShowDialog() == DialogResult.OK)
    {
        // Podatke čitamo iz javnih svojstava forme za unos
        lstUcenici.Items.Add(unos.Prezime + " " + unos.Ime + ", " + unos.Odeljenje);
        OsveziBroj();
    }
}
```

### Korak 9. Proverite

- Dodajte pet učenika; za svakog unos potvrdite tasterom Enter.
- Otvorite formu za unos i pritisnite Esc: lista se ne menja.
- Ostavite jedno polje prazno i kliknite U redu: pojavljuje se poruka, a forma ostaje otvorena sa upisanim podacima.
- Probno u Form1 napišite unos.txtIme.Text i pročitajte grešku koju prijavljuje Visual Studio. Zatim obrišite taj red.

## Čas 12 – Slanje podataka u formu

Forma za unos vraća podatke glavnoj formi kroz svojstva, posle zatvaranja. Forma sa detaljima ima obrnut zadatak: mora da zna o kom učeniku je reč već u trenutku kada se otvori.

**1. Konstruktor sa parametrom.** Konstruktor je metoda koja se izvršava kada se piše new frmDetalji(...). Visual Studio pravi konstruktor bez parametara; mi ga menjamo tako da prima podatke:

```csharp
public frmDetalji(string podaci)
{
    InitializeComponent();          // prvo se naprave kontrole
    lblUcenik.Text = podaci;        // tek onda se mogu popuniti
}
```

Red InitializeComponent() mora ostati prvi: on pravi sve kontrole sa forme. Pre njega lblUcenik još ne postoji.

Glavna forma podatke šalje u zagradama, kao kod poziva metode:

```csharp
frmDetalji detalji = new frmDetalji(lstUcenici.SelectedItem.ToString());
detalji.Show();
```

Pošto je forma nemodalna, za dva različita učenika mogu istovremeno biti otvorena dva prozora detalja, svaki sa svojim podacima.

**2. Vlasnik prozora: Owner.** Kada nemodalnoj formi postavite detalji.Owner = this;, glavna forma postaje njen vlasnik. Prozor detalja tada uvek stoji iznad glavne forme, a kada se glavna forma umanji ili zatvori, i on se umanji ili zatvori.

**3. Kako forme razmenjuju podatke – pregled**

| Smer | Način | Kada | Primer u Evidenciji |
| --- | --- | --- | --- |
| u formu, pri otvaranju | parametar konstruktora | forma mora da ima podatke od samog početka | new frmDetalji("Petrović Marko, IV-2") |
| iz forme, posle zatvaranja | javno svojstvo | modalna forma vraća ono što je korisnik upisao | unos.Ime |
| koje je dugme kliknuto | DialogResult | modalna forma javlja da li je korisnik potvrdio | unos.ShowDialog() == DialogResult.OK |

## Čas 12 – Izrada: forma sa detaljima

### Korak 10. Forma za detalje

Project → Add Windows Form, Name: frmDetalji.cs.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| Form | frmDetalji | Text: Detalji; Size: 340; 200; FormBorderStyle: FixedSingle; MaximizeBox: False; StartPosition: CenterScreen |
| Label | lblUcenik | Font: Size 14, Bold |
| Label | lblOdeljenje | (podrazumevano) |
| Label | lblOtvoreno | ForeColor: Gray |
| Button | btnZatvori | Text: Zatvori |

Nemodalna forma ne koristi CenterParent (to radi samo sa ShowDialog), nego CenterScreen.

### Korak 11. Konstruktor koji prima podatke

U kodu frmDetalji zamenite postojeći konstruktor ovim i dvoklikom na btnZatvori napravite njegovu metodu za obradu događaja:

```csharp
public frmDetalji(string podaci)
{
    InitializeComponent();

    // podaci izgledaju ovako: "Petrović Marko, IV-2"
    string[] delovi = podaci.Split(',');
    lblUcenik.Text = delovi[0];
    lblOdeljenje.Text = "Odeljenje: " + delovi[1].Trim();
    lblOtvoreno.Text = "Otvoreno u " + DateTime.Now.ToString("HH:mm:ss");
    this.Text = "Detalji - " + delovi[0];
}

private void btnZatvori_Click(object sender, EventArgs e)
{
    this.Close();
}
```

Split(',') deli tekst na mestu zareza: delovi\[0\] je „Petrović Marko“, a delovi\[1\] je „ IV-2“ (sa razmakom na početku, koji uklanja Trim). Vreme otvaranja pomaže da se razlikuju dva otvorena prozora.

### Korak 12. Otvaranje detalja iz glavne forme

U Form1 napravite metodu za obradu događaja btnDetalji\_Click (dvoklik), a za lstUcenici u kartici Events dvoklikom napravite metodu za obradu događaja DoubleClick. Oba pozivaju istu metodu:

```csharp
private void btnDetalji_Click(object sender, EventArgs e)
{
    PrikaziDetalje();
}

private void lstUcenici_DoubleClick(object sender, EventArgs e)
{
    PrikaziDetalje();
}

private void PrikaziDetalje()
{
    if (lstUcenici.SelectedIndex == -1)
    {
        MessageBox.Show("Izaberite učenika u listi.", "Detalji",
            MessageBoxButtons.OK, MessageBoxIcon.Information);
        return;
    }

    // Podaci se prosleđuju kroz konstruktor
    frmDetalji detalji = new frmDetalji(lstUcenici.SelectedItem.ToString());
    detalji.Owner = this;   // prozor ostaje iznad glavne forme i zatvara se sa njom
    detalji.Show();
}
```

**Proverite:** otvorite detalje za dva različita učenika (dva prozora istovremeno); umanjite glavnu formu i pogledajte šta se desi sa prozorima detalja; kliknite Detalji kada ništa nije izabrano.

### Ceo kod

**Form1.cs**

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace EvidencijaUcenika
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            OsveziBroj();
        }

        private void OsveziBroj()
        {
            lblBroj.Text = "Ukupno učenika: " + lstUcenici.Items.Count;
        }

        // ---------- Modalna forma za unos (čas 11) ----------

        private void btnDodaj_Click(object sender, EventArgs e)
        {
            frmUnos unos = new frmUnos();
            if (unos.ShowDialog() == DialogResult.OK)
            {
                // Podatke čitamo iz javnih svojstava forme za unos
                lstUcenici.Items.Add(unos.Prezime + " " + unos.Ime + ", " + unos.Odeljenje);
                OsveziBroj();
            }
        }

        // ---------- Nemodalna forma sa detaljima (čas 12) ----------

        private void btnDetalji_Click(object sender, EventArgs e)
        {
            PrikaziDetalje();
        }

        private void lstUcenici_DoubleClick(object sender, EventArgs e)
        {
            PrikaziDetalje();
        }

        private void PrikaziDetalje()
        {
            if (lstUcenici.SelectedIndex == -1)
            {
                MessageBox.Show("Izaberite učenika u listi.", "Detalji",
                    MessageBoxButtons.OK, MessageBoxIcon.Information);
                return;
            }

            // Podaci se prosleđuju kroz konstruktor
            frmDetalji detalji = new frmDetalji(lstUcenici.SelectedItem.ToString());
            detalji.Owner = this;   // prozor ostaje iznad glavne forme i zatvara se sa njom
            detalji.Show();
        }

        // ---------- Uputstvo i O programu (čas 10) ----------

        private void btnUputstvo_Click(object sender, EventArgs e)
        {
            frmUputstvo uputstvo = new frmUputstvo();
            uputstvo.Show();          // nemodalno: može se raditi i u glavnoj formi
        }

        private void btnOProgramu_Click(object sender, EventArgs e)
        {
            frmOProgramu oProgramu = new frmOProgramu();
            oProgramu.ShowDialog();   // modalno: glavna forma čeka da se ovaj prozor zatvori
        }
    }
}
```

**frmUnos.cs** (bez using redova, koji su isti kao u Form1.cs)

```csharp
namespace EvidencijaUcenika
{
    public partial class frmUnos : Form
    {
        public frmUnos()
        {
            InitializeComponent();
        }

        // Javna svojstva: glavna forma preko njih čita unete podatke
        public string Ime
        {
            get { return txtIme.Text.Trim(); }
        }

        public string Prezime
        {
            get { return txtPrezime.Text.Trim(); }
        }

        public string Odeljenje
        {
            get { return txtOdeljenje.Text.Trim(); }
        }

        private void btnUredu_Click(object sender, EventArgs e)
        {
            if (Ime == "" || Prezime == "" || Odeljenje == "")
            {
                MessageBox.Show("Popunite sva polja.", "Novi učenik",
                    MessageBoxButtons.OK, MessageBoxIcon.Warning);
                return;   // forma ostaje otvorena
            }
            this.DialogResult = DialogResult.OK;   // zatvara formu; ShowDialog vraća OK
        }
    }
}
```

**frmDetalji.cs**

```csharp
namespace EvidencijaUcenika
{
    public partial class frmDetalji : Form
    {
        public frmDetalji(string podaci)
        {
            InitializeComponent();

            // podaci izgledaju ovako: "Petrović Marko, IV-2"
            string[] delovi = podaci.Split(',');
            lblUcenik.Text = delovi[0];
            lblOdeljenje.Text = "Odeljenje: " + delovi[1].Trim();
            lblOtvoreno.Text = "Otvoreno u " + DateTime.Now.ToString("HH:mm:ss");
            this.Text = "Detalji - " + delovi[0];
        }

        private void btnZatvori_Click(object sender, EventArgs e)
        {
            this.Close();
        }
    }
}
```

**frmOProgramu.cs** ima samo metodu za obradu događaja btnZatvori\_Click iz koraka 2, a **frmUputstvo.cs** nema dodatnog koda.

## Rezime

- Nova forma se dodaje sa Project → Add Windows Form; prozor se pravi sa **new** i prikazuje sa **Show()** ili **ShowDialog()**.
- **ShowDialog()** je modalni prikaz: ostatak programa čeka, a metoda vraća DialogResult. **Show()** je nemodalni prikaz: oba prozora rade, a istu formu možete otvoriti više puta.
- Forma za unos vraća odgovor kroz **DialogResult** (dugme Odustani u Properties, U redu u kodu posle provere), a podatke kroz **javna svojstva** sa delom get. Enter i Esc rade preko **AcceptButton** i **CancelButton**.
- Podaci se šalju u formu pri otvaranju kroz **parametar konstruktora**; InitializeComponent() ostaje prvi red. **Owner** vezuje nemodalni prozor za glavnu formu.

## Pitanja za samoproveru

1. Navedite po jedan primer modalnog i nemodalnog prozora iz programa koje koristite i objasnite zašto je takav.
2. Zašto dugmetu U redu ne postavljamo DialogResult u Properties, a dugmetu Odustani postavljamo?
3. Glavna forma ne može da napiše unos.txtIme.Text. Zašto, i kako ipak dobija ime?
4. Šta bi se desilo kada bi se forma za unos otvorila sa Show() umesto ShowDialog()?
5. Zašto u konstruktoru forme InitializeComponent() mora biti prvi red?

## Zadaci

U svim zadacima ime nove forme zadajte pri dodavanju (prefiks frm). Zadaci 2, 5, 6 i 8 nadograđuju ranije projekte: pre izmena napravite kopiju.

| Zadatak | Nivo | Kada |
| --- | --- | --- |
| 1. Lepljive beleške | \[OSNOVNI\] | na času 10 ko završi Evidenciju, ostali za domaći |
| 2. Uputstvo samo jednom | \[SREDNJI\] | domaći posle časa 10 |
| 3. Prijava | \[OSNOVNI\] | na času 11 ko završi Evidenciju, ostali za domaći |
| 4. Prodavnica | \[SREDNJI\] | domaći posle časa 11 |
| 5. Lepljive beleške sa brojem | \[OSNOVNI\] | na času 12 ko završi Evidenciju, ostali za domaći |
| 6. Izmena učenika | \[SREDNJI\] | domaći posle časa 12 |
| 7. Kviz | \[NAPREDNI\] | domaći posle časa 12 |
| 8. Pretraga | \[NAPREDNI\] | domaći posle časa 12 |

### Zadatak 1 \[OSNOVNI\] – Lepljive beleške

Glavna forma ima dugmad Nova beleška i O programu. Nova beleška otvara mali žuti prozor sa višerednim poljem za tekst (forma frmBeleska); svaki klik otvara novu belešku, a u glavnoj formi se i dalje radi. O programu otvara modalni prozor sa nazivom programa i dugmetom Zatvori. Podesite ga kao dijalog-formu.

### Zadatak 2 \[SREDNJI\] – Uputstvo samo jednom

U Evidenciji svaki klik na Uputstvo otvara novi prozor. Izmenite program tako da se Uputstvo otvori samo jednom: ako je već otvoreno, drugi klik ga samo dovede u prvi plan (i vrati iz umanjenog stanja). Ako ga korisnik zatvori, sledeći klik ga ponovo otvara. Savet: promenljiva sa formom treba da bude polje klase; zatvorena nemodalna forma ima IsDisposed = true; metoda Activate() dovodi prozor u prvi plan.

### Zadatak 3 \[OSNOVNI\] – Prijava

Pre nego što se glavna forma pojavi, otvara se modalna forma za prijavu sa poljima Korisničko ime i Lozinka (PasswordChar: \*) i dugmadima Prijavi se i Odustani. Lozinka je „pupin“; pogrešna lozinka daje poruku i forma ostaje otvorena. Posle uspešne prijave glavna forma ispisuje „Dobrodošli, ime!“; ako korisnik odustane, program se završava. Savet: prijavu otvorite u Form1\_Load; korisničko ime vratite kroz svojstvo.

### Zadatak 4 \[SREDNJI\] – Prodavnica

Glavna forma ima korpu (listu), dugme Novi proizvod... i labelu sa ukupnom cenom. Dugme otvara modalnu formu sa poljima Naziv i Cena. Forma ne sme da se zatvori sa praznim nazivom ili sa cenom koja nije pozitivan broj. Proizvod se u korpu upisuje kao „Hleb - 75,00 din“, a ukupna cena se uvećava. Savet: svojstvo Cena neka bude tipa double.

### Zadatak 5 \[OSNOVNI\] – Lepljive beleške sa brojem

Izmenite Zadatak 1: svaka nova beleška u naslovu ima svoj redni broj (Beleška 1, Beleška 2...), a svaka druga beleška je zelena umesto žute. Broj prosledite kroz konstruktor. Postavite i Owner, pa proverite šta se desi sa beleškama kada umanjite glavnu formu.

### Zadatak 6 \[SREDNJI\] – Izmena učenika

Evidenciji dodajte dugme Izmeni.... Ono otvara istu formu frmUnos, ali sa naslovom „Izmena učenika“ i sa već popunjenim poljima izabranog učenika. Ako korisnik potvrdi izmenu, red u listi se zamenjuje novim podacima. Savet: svojstvima u frmUnos dodajte i deo set; red u listi menjate sa lstUcenici.Items\[indeks\] = ....

### Zadatak 7 \[NAPREDNI\] – Kviz

Napravite kviz od tri pitanja o formama. Klik na Počni kviz redom otvara modalnu formu za svako pitanje: tekst pitanja i redni broj („Pitanje 2 od 3“) forma dobija kroz konstruktor, a odgovor vraća kroz svojstvo. Dugme Prekini kviz zaustavlja kviz. Na kraju se otvara nemodalna forma sa rezultatom („Tačnih odgovora: 2 od 3“) i porukom koja zavisi od uspeha. Velika i mala slova u odgovoru ne treba da utiču na tačnost.

### Zadatak 8 \[NAPREDNI\] – Pretraga

Evidenciji dodajte dugme Pretraga koje otvara mali nemodalni prozor sa poljem i dugmetom Nađi. Klik na Nađi u listi glavne forme izabere prvog učenika čije ime ili prezime sadrži upisani tekst, a u prozoru za pretragu ispiše da li je učenik pronađen. Savet: u Form1 napišite javnu metodu IzaberiUcenika(string deoImena); prozor za pretragu do glavne forme dolazi preko svojstva Owner.
