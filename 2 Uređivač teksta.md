# Susret 2 – Materijal za učenike: Uređivač teksta

Oct 18, 2026 · @Vanja

## Šta pravimo

Za tri časa napravićete „Uređivač teksta“, mali program nalik Notepadu: pišete tekst, menjate mu izgled i u dnu prozora vidite broj karaktera i reči. Iste komande biće i u meniju i na paleti komandi, kao u Wordu.

| Čas | Šta učimo | Šta dodajemo u Uređivač |
| --- | --- | --- |
| 4 | paleta komandi (ToolStrip) | dugmad Novo, B, I, Kopiraj sve i listu za veličinu slova |
| 5 | ista komanda iz menija i sa palete | glavni meni sa istim komandama; meni i paleta uvek pokazuju isto stanje |
| 6 | traka statusa (StatusStrip); meni i paleta | broj karaktera i reči; sakrivanje traka; boja slova (samostalno) |

**Imenovanje.** Kao na susretu 1, uz dva nova prefiksa: tsb za dugme na paleti i tsc za padajuću listu na paleti. Oznake u traci statusa imaju prefiks lbl, jer su to labele.

## Čas 4 – Paleta komandi

Paleta komandi (ToolStrip) je traka sa dugmadima ispod menija. Na njoj su samo komande koje se najčešće koriste, i svaka se izvršava jednim klikom. U Wordu su to npr. dugmad B, I, U i lista za veličinu slova.

**Šta može da stoji na paleti**

| Element | Klasa | Primer u Uređivaču |
| --- | --- | --- |
| dugme | ToolStripButton | Novo, B, I, Kopiraj sve |
| padajuća lista | ToolStripComboBox | veličina slova |
| separator (uspravna crta) | ToolStripSeparator | razmak između grupa dugmadi |

**Važna svojstva**

| Svojstvo | Šta radi | Primer |
| --- | --- | --- |
| DisplayStyle | šta dugme prikazuje: Image (sliku), Text (tekst), ImageAndText (oba) ili None | Text |
| ToolTipText | opis koji se pojavi kada se miš zadrži nad dugmetom | Podebljano (Ctrl+B) |
| Checked | dugme je utisnuto (uključeno) | B dok je tekst podebljan |
| Enabled | False: dugme je sivo | Kopiraj sve dok nema teksta |
| DropDownStyle (lista) | DropDownList: korisnik samo bira, ne može da kuca | lista veličina |
| Items (lista) | stavke liste, svaka u svom redu | 10, 12, 14, 18, 24 |

Novo dugme ima DisplayStyle = Image i praznu podrazumevanu sličicu. Pošto nemamo ikonice, koristimo DisplayStyle = Text. Ko želi sliku, može je uvesti preko svojstva Image (dugme Import).

**Kako se pravi paleta**

1. Iz grupe Menus & Toolbars prevucite ToolStrip na formu. Traka se smesti uz gornju ivicu.
2. Kliknite na strelicu na paleti i izaberite šta dodajete: Button, Separator ili ComboBox.
3. Svakom elementu u prozoru Properties prvo postavite Name, a zatim ostala svojstva.

**Ime metode za obradu događaja upisujemo sami.** Na času 5 ista komanda će se pozivati i iz menija, pa metoda za obradu događaja ne treba da nosi ime dugmeta. Izaberite dugme, otvorite karticu Events (munja), u polje pored Click upišite ime, npr. Podebljano\_Click, i pritisnite Enter. Visual Studio napravi metodu za obradu događaja sa tim imenom.

## Čas 4 – Izrada: Uređivač sa paletom komandi

### Korak 1. Novi projekat

Napravite novi projekat Windows Forms Application pod imenom UredjivacTeksta. Formi postavite Text: Uređivač teksta, Size: 640; 480, StartPosition: CenterScreen.

### Korak 2. Paleta komandi

Dodajte ToolStrip i na njega, redom, elemente iz tabele.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| ToolStrip | toolStrip1 | (podrazumevano) |
| dugme | tsbNovo | Text: Novo; DisplayStyle: Text; ToolTipText: Novi tekst (Ctrl+N) |
| separator | toolStripSeparator1 | (podrazumevano) |
| dugme | tsbPodebljano | Text: B; DisplayStyle: Text; Font: Bold; ToolTipText: Podebljano (Ctrl+B) |
| dugme | tsbKurziv | Text: I; DisplayStyle: Text; Font: Italic; ToolTipText: Kurziv (Ctrl+I) |
| separator | toolStripSeparator2 | (podrazumevano) |
| padajuća lista | tscVelicina | DropDownStyle: DropDownList; Items: 10, 12, 14, 18, 24 (svaki broj u svom redu); Size: 50; 25; ToolTipText: Veličina slova |
| separator | toolStripSeparator3 | (podrazumevano) |
| dugme | tsbKopirajSve | Text: Kopiraj sve; DisplayStyle: Text; Enabled: False; ToolTipText: Kopiraj ceo tekst (Ctrl+Shift+C) |

Prečice iz opisa (Ctrl+B i ostale) proradiće na času 5, kada dodamo meni.

### Korak 3. Polje za tekst

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| TextBox | txtTekst | Multiline: True; Dock: Fill; ScrollBars: Vertical; Font: Segoe UI, 12 |

Polje zauzima ceo ostatak forme. Ako prvi red teksta nestane ispod palete, kliknite desnim tasterom na polje i izaberite **Bring to Front**. Isto uradite svaki put kada dodate novu traku (meni na času 5, traku statusa na času 6).

### Korak 4. Metode za obradu događaja

Metode za obradu događaja pravite ovako (kartica Events, upišite ime u polje pored događaja, pa Enter):

| Kontrola | Događaj | Ime metode za obradu događaja |
| --- | --- | --- |
| tsbNovo | Click | Novo\_Click |
| tsbPodebljano | Click | Podebljano\_Click |
| tsbKurziv | Click | Kurziv\_Click |
| tsbKopirajSve | Click | KopirajSve\_Click |
| tscVelicina | SelectedIndexChanged | dvoklik na listu: tscVelicina\_SelectedIndexChanged |
| txtTekst | TextChanged | dvoklik na polje: txtTekst\_TextChanged |
| Form1 | Load | dvoklik na naslovnu traku forme: Form1\_Load |

```csharp
private void Form1_Load(object sender, EventArgs e)
{
    tscVelicina.SelectedIndex = 1;   // 12
    OsveziStanje();
}

private void Novo_Click(object sender, EventArgs e)
{
    if (txtTekst.Text != "")
    {
        DialogResult odgovor = MessageBox.Show("Obrisati tekst i početi novi?", "Novo",
            MessageBoxButtons.YesNo, MessageBoxIcon.Question);
        if (odgovor == DialogResult.No)
        {
            return;
        }
    }
    txtTekst.Clear();
    txtTekst.Focus();
}

private void KopirajSve_Click(object sender, EventArgs e)
{
    Clipboard.SetText(txtTekst.Text);
}

private void Podebljano_Click(object sender, EventArgs e)
{
    tsbPodebljano.Checked = !tsbPodebljano.Checked;
    PrimeniStil();
}

private void Kurziv_Click(object sender, EventArgs e)
{
    tsbKurziv.Checked = !tsbKurziv.Checked;
    PrimeniStil();
}

private void PrimeniStil()
{
    FontStyle stil = FontStyle.Regular;
    if (tsbPodebljano.Checked)
    {
        stil = stil | FontStyle.Bold;
    }
    if (tsbKurziv.Checked)
    {
        stil = stil | FontStyle.Italic;
    }
    txtTekst.Font = new Font(txtTekst.Font.FontFamily, txtTekst.Font.Size, stil);
}

private void tscVelicina_SelectedIndexChanged(object sender, EventArgs e)
{
    PostaviVelicinu(int.Parse(tscVelicina.Text));
}

private void PostaviVelicinu(int velicina)
{
    txtTekst.Font = new Font(txtTekst.Font.FontFamily, velicina, txtTekst.Font.Style);
}

private void txtTekst_TextChanged(object sender, EventArgs e)
{
    OsveziStanje();
}

private void OsveziStanje()
{
    // Kopiraj sve ima smisla samo kada postoji tekst
    bool imaTeksta = txtTekst.Text != "";
    tsbKopirajSve.Enabled = imaTeksta;
}
```

### Korak 5. Šta znače ključni redovi

- **tsbPodebljano.Checked = !tsbPodebljano.Checked;** okreće stanje dugmeta: ako je bilo utisnuto, više nije, i obrnuto. Znak ! znači „ne“.
- **Font se ne može menjati deo po deo.** Ne može se napisati txtTekst.Font.Bold = true. Zato pravimo novi font: new Font(porodica, veličina, stil).
- **stil | FontStyle.Bold** dodaje podebljanje stilu koji već imamo. Tako tekst može biti istovremeno i podebljan i u kurzivu.
- **tscVelicina.SelectedIndex = 1** u Form1\_Load bira drugu stavku liste (12). Time se pokreće SelectedIndexChanged i font dobija veličinu 12.
- **OsveziStanje()** se poziva posle svake promene teksta i uključuje ili isključuje Kopiraj sve. Kopiranje praznog teksta bi izazvalo grešku, kao na susretu 1.

**Proverite:** zadržite miš nad svakim dugmetom i pročitajte opis; uključite B i I zajedno; promenite veličinu; obrišite sav tekst i proverite da je Kopiraj sve sivo.

## Čas 5 – Ista komanda iz menija i sa palete

U Wordu Podebljano postoji i u meniju i na paleti, a radi isto. Kad ga uključite na paleti, uključeno je i u meniju. Isto želimo i u našem programu, bez pisanja istog koda dva puta.

**1. Zajednička metoda za obradu događaja.** Ako stavka menija i dugme palete imaju isti događaj (Click), mogu da koriste istu metodu za obradu događaja. Izaberite stavku menija, otvorite karticu Events i kod događaja Click iz padajuće liste izaberite postojeću metodu za obradu događaja, npr. Podebljano\_Click. Ne radite dvoklik: on bi napravio novu, praznu metodu za obradu događaja.

**2. Usklađeno stanje.** Kada isti kod poziva i meni i paleta, oba mesta moraju da pokazuju isto stanje. Ako je dugme B utisnuto, i stavka Podebljano mora imati kvačicu. Zato u metodi za obradu događaja menjamo stanje na oba mesta:

```csharp
tsbPodebljano.Checked = !tsbPodebljano.Checked;
mnuPodebljano.Checked = tsbPodebljano.Checked;   // isto stanje i u meniju
```

Isto važi i za Enabled: kada je Kopiraj sve sivo na paleti, mora biti sivo i u meniju.

**3. Ko je kliknuo: parametar sender.** Svaka metoda za obradu događaja ima parametar sender: to je kontrola koja je izazvala događaj. Kada pet stavki menija za veličinu slova koristi jednu metodu za obradu događaja Velicina\_Click, preko sender saznajemo na koju je stavku kliknuto. Pošto je sender opšteg tipa object, prvo ga pretvaramo u stavku menija:

```csharp
ToolStripMenuItem stavka = (ToolStripMenuItem)sender;
PostaviVelicinu(int.Parse(stavka.Text));   // natpis stavke je broj, npr. "14"
```

**4. Kada događaji nisu isti: zajednička metoda.** Stavka menija ima događaj Click, a padajuća lista SelectedIndexChanged. Oni ne mogu da dele metodu za obradu događaja, ali oba mogu da pozovu istu metodu, PostaviVelicinu. Posao se tako i dalje piše samo jednom.

**5. Prolazak kroz stavke podmenija.** Petlja foreach prolazi kroz sve stavke podmenija (svojstvo DropDownItems). Ovako štikliramo samo stavku izabrane veličine:

```csharp
foreach (ToolStripMenuItem stavka in mnuVelicina.DropDownItems)
{
    stavka.Checked = stavka.Text == velicina.ToString();
}
```

Izraz stavka.Text == velicina.ToString() je true samo za stavku sa izabranim brojem. Pažnja: u ovom podmeniju ne sme biti separatora, jer separator nije ToolStripMenuItem i petlja bi izazvala grešku.

## Čas 5 – Izrada: meni sa istim komandama

### Korak 6. Glavni meni

Dodajte MenuStrip i napravite menije iz tabele. Posle toga uradite **Bring to Front** na polju txtTekst. U poslednjoj koloni je metoda za obradu događaja koju birate u kartici Events: za stavke koje dele kod ne radite dvoklik.

| Kontrola | Name | Svojstva | Click |
| --- | --- | --- | --- |
| MenuStrip | menuStrip1 | (podrazumevano) |  |
| meni | mnuDatoteka | Text: &Datoteka |  |
| stavka | mnuNovo | Text: \&Novo; ShortcutKeys: Ctrl+N | Novo\_Click |
| separator | toolStripSeparator4 | (upisati „-“) |  |
| stavka | mnuIzlaz | Text: \&Izlaz; ShortcutKeys: Ctrl+Q | dvoklik: mnuIzlaz\_Click |
| meni | mnuUredjivanje | Text: &Uređivanje |  |
| stavka | mnuKopirajSve | Text: &Kopiraj sve; ShortcutKeys: Ctrl+Shift+C; Enabled: False | KopirajSve\_Click |
| meni | mnuFormat | Text: &Format |  |
| stavka | mnuPodebljano | Text: \&Podebljano; ShortcutKeys: Ctrl+B | Podebljano\_Click |
| stavka | mnuKurziv | Text: \&Kurziv; ShortcutKeys: Ctrl+I | Kurziv\_Click |
| separator | toolStripSeparator5 | (upisati „-“) |  |
| stavka | mnuVelicina | Text: &Veličina slova |  |
| stavka u podmeniju Veličina | mnuVel10 | Text: 10 | upisati: Velicina\_Click |
| stavka u podmeniju Veličina | mnuVel12 | Text: 12 | Velicina\_Click |
| stavka u podmeniju Veličina | mnuVel14 | Text: 14 | Velicina\_Click |
| stavka u podmeniju Veličina | mnuVel18 | Text: 18 | Velicina\_Click |
| stavka u podmeniju Veličina | mnuVel24 | Text: 24 | Velicina\_Click |
| meni | mnuPomoc | Text: &Pomoć |  |
| stavka | mnuOProgramu | Text: &O programu; ShortcutKeys: F1 | dvoklik: mnuOProgramu\_Click |

Metoda za obradu događaja Velicina\_Click ne postoji, pa je kod prve stavke (mnuVel10) napravite upisom imena. Za ostale četiri stavke je izaberite iz liste.

### Korak 7. Usklađivanje stanja

Dopunite postojeće metode. Novi redovi su označeni komentarom.

```csharp
private void Podebljano_Click(object sender, EventArgs e)
{
    tsbPodebljano.Checked = !tsbPodebljano.Checked;
    mnuPodebljano.Checked = tsbPodebljano.Checked;   // NOVO: isto stanje i u meniju
    PrimeniStil();
}

private void Kurziv_Click(object sender, EventArgs e)
{
    tsbKurziv.Checked = !tsbKurziv.Checked;
    mnuKurziv.Checked = tsbKurziv.Checked;           // NOVO
    PrimeniStil();
}

private void OsveziStanje()
{
    bool imaTeksta = txtTekst.Text != "";
    tsbKopirajSve.Enabled = imaTeksta;
    mnuKopirajSve.Enabled = imaTeksta;               // NOVO
}
```

### Korak 8. Veličina slova iz menija

```csharp
// Zajednička metoda za obradu događaja za svih pet stavki podmenija Veličina slova
private void Velicina_Click(object sender, EventArgs e)
{
    ToolStripMenuItem stavka = (ToolStripMenuItem)sender;
    PostaviVelicinu(int.Parse(stavka.Text));
}

private void PostaviVelicinu(int velicina)
{
    txtTekst.Font = new Font(txtTekst.Font.FontFamily, velicina, txtTekst.Font.Style);

    // NOVO: štikliramo samo stavku sa izabranom veličinom
    foreach (ToolStripMenuItem stavka in mnuVelicina.DropDownItems)
    {
        stavka.Checked = stavka.Text == velicina.ToString();
    }
    tscVelicina.SelectedItem = velicina.ToString();   // NOVO: ista veličina i na paleti
}
```

Kada izaberete veličinu u meniju, PostaviVelicinu postavlja istu vrednost i u padajućoj listi. Kada je izaberete u listi, ista metoda štiklira stavku u meniju. Zato natpisi stavki menija i stavke liste moraju biti potpuno isti: samo broj.

### Korak 9. Izlaz i O programu

```csharp
private void mnuIzlaz_Click(object sender, EventArgs e)
{
    this.Close();
}

private void mnuOProgramu_Click(object sender, EventArgs e)
{
    MessageBox.Show("Uređivač teksta, verzija 1.0\nAutor: Marko Marković, IV-2",
        "O programu", MessageBoxButtons.OK, MessageBoxIcon.Information);
}
```

**Proverite:** Ctrl+B utiskuje dugme B, a klik na B stavlja kvačicu u meniju; veličina izabrana u meniju pojavljuje se u listi na paleti, i obrnuto; Kopiraj sve je sivo na oba mesta dok nema teksta.

## Čas 6 – Traka statusa; meni i paleta komandi

Traka statusa (StatusStrip) je traka na dnu prozora koja pokazuje podatke o onome što korisnik radi. U Wordu tu piše broj stranica i broj reči. Korisnik na nju ne klikće, samo je čita.

**Kako se pravi traka statusa**

1. Iz grupe Menus & Toolbars prevucite StatusStrip na formu. Smesti se uz donju ivicu.
2. Kliknite na strelicu na traci i izaberite StatusLabel. To je oznaka (ToolStripStatusLabel) koja se ponaša kao obična labela: menjamo joj svojstvo Text.
3. Uradite Bring to Front na polju txtTekst.

**Brojanje reči: metoda Split.** Metoda Split deli tekst na delove na mestima zadatih znakova i vraća niz tih delova. Reči su razdvojene razmakom, tabulatorom ili prelazom u novi red. Opcija StringSplitOptions.RemoveEmptyEntries izbacuje prazne delove, koji nastaju kada između reči stoje dva razmaka.

```csharp
string[] reci = txtTekst.Text.Split(new char[] { ' ', '\r', '\n', '\t' },
    StringSplitOptions.RemoveEmptyEntries);
lblReci.Text = "Reči: " + reci.Length;
```

**Sakrivanje traka: svojstvo Visible.** Paleta i traka statusa zauzimaju prostor, pa ih programi obično mogu sakriti iz menija Prikaz. Stavka sa CheckOnClick = True menja kvačicu sama, a u njenom događaju CheckedChanged postavljamo toolStrip1.Visible = mnuPaleta.Checked. Obrnuto ne može: meni se ne sakriva, jer bez njega korisnik ne bi mogao da vrati paletu.

### Meni ili paleta komandi? (rad u paru)

Sa drugom iz klupe popunite tabelu. Proverićemo je zajedno.

| Pitanje | Glavni meni | Paleta komandi |
| --- | --- | --- |
| Koje komande sadrži? |  |  |
| Kako izgleda komanda? |  |  |
| Koliko klikova treba do komande? |  |  |
| Kako korisnik saznaje šta komanda radi? |  |  |
| Da li se može sakriti? |  |  |
| Može li se koristiti samo tastaturom? |  |  |
| Kako se klase zovu u Visual Studiju? |  |  |
| Kako se vidi da je opcija uključena? |  |  |

Koju od ovih komandi biste stavili i na paletu, a koju samo u meni: Snimi, O programu, Podebljano, Podešavanja štampača? Obrazložite.

## Čas 6 – Izrada: traka statusa, meni Prikaz i boja slova

### Korak 10. Traka statusa

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| StatusStrip | statusStrip1 | (podrazumevano) |
| oznaka trake statusa | lblKarakteri | Text: Karaktera: 0 |
| oznaka trake statusa | lblReci | Text: Reči: 0 |

Ne zaboravite Bring to Front na polju txtTekst.

### Korak 11. Brojanje karaktera i reči

Dopunite metodu OsveziStanje sa dva nova dela:

```csharp
private void OsveziStanje()
{
    bool imaTeksta = txtTekst.Text != "";
    tsbKopirajSve.Enabled = imaTeksta;
    mnuKopirajSve.Enabled = imaTeksta;

    // NOVO: prelaz u novi red (\r\n) ne brojimo kao karaktere
    lblKarakteri.Text = "Karaktera: " + txtTekst.Text.Replace("\r\n", "").Length;

    // NOVO: broj reči
    string[] reci = txtTekst.Text.Split(new char[] { ' ', '\r', '\n', '\t' },
        StringSplitOptions.RemoveEmptyEntries);
    lblReci.Text = "Reči: " + reci.Length;
}
```

Kada pritisnete Enter, u tekst ulaze dva nevidljiva znaka, \\r i \\n. Zato ih pre brojanja karaktera uklanjamo.

### Korak 12. Meni Prikaz

Dodajte meni između menija Format i Pomoć.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| meni | mnuPrikaz | Text: P&rikaz |
| stavka | mnuPaleta | Text: &Paleta komandi; CheckOnClick: True; Checked: True |
| stavka | mnuTrakaStatusa | Text: &Traka statusa; CheckOnClick: True; Checked: True |

Za obe stavke u kartici Events dvoklikom napravite metodu za obradu događaja CheckedChanged:

```csharp
private void mnuPaleta_CheckedChanged(object sender, EventArgs e)
{
    toolStrip1.Visible = mnuPaleta.Checked;
}

private void mnuTrakaStatusa_CheckedChanged(object sender, EventArgs e)
{
    statusStrip1.Visible = mnuTrakaStatusa.Checked;
}
```

Meni Prikaz ima pristupni taster R (P&rikaz), jer slovo P već koristi meni Pomoć.

### Korak 13. Boja slova – samostalno

Dodajte boju slova i u meni i na paletu, **po uzoru na veličinu slova**. Ovo radite sami.

| Kontrola | Name | Svojstva |
| --- | --- | --- |
| padajuća lista na paleti, posle tscVelicina | tscBoja | DropDownStyle: DropDownList; Items: Crna, Plava, Crvena, Zelena; Size: 80; 25; ToolTipText: Boja slova |
| stavka u meniju Format, ispod Veličina slova | mnuBoja | Text: &Boja slova |
| stavke u podmeniju Boja slova | mnuCrna, mnuPlava, mnuCrvena, mnuZelena | Text: Crna, Plava, Crvena, Zelena; Click: Boja\_Click |

Uputstvo:

1. Napišite metodu PostaviBoju(string boja) koja menja txtTekst.ForeColor (Color.Black, Color.Blue, Color.Red, Color.Green), štiklira odgovarajuću stavku podmenija i bira istu boju u listi tscBoja.
2. Metoda za obradu događaja Boja\_Click preko sender saznaje natpis kliknute stavke i poziva PostaviBoju.
3. Metoda za obradu događaja tscBoja\_SelectedIndexChanged poziva PostaviBoju(tscBoja.Text).
4. U Form1\_Load dodajte tscBoja.SelectedIndex = 0;

Kada završite, uporedite svoje rešenje sa celim kodom ispod.

### Ceo kod Form1.cs

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace UredjivacTeksta
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            tscVelicina.SelectedIndex = 1;   // 12
            tscBoja.SelectedIndex = 0;       // Crna
            OsveziStanje();
        }

        // ---------- Datoteka i Uređivanje (meni i paleta dele metode za obradu događaja) ----------

        private void Novo_Click(object sender, EventArgs e)
        {
            if (txtTekst.Text != "")
            {
                DialogResult odgovor = MessageBox.Show("Obrisati tekst i početi novi?", "Novo",
                    MessageBoxButtons.YesNo, MessageBoxIcon.Question);
                if (odgovor == DialogResult.No)
                {
                    return;
                }
            }
            txtTekst.Clear();
            txtTekst.Focus();
        }

        private void KopirajSve_Click(object sender, EventArgs e)
        {
            Clipboard.SetText(txtTekst.Text);
        }

        private void mnuIzlaz_Click(object sender, EventArgs e)
        {
            this.Close();
        }

        // ---------- Podebljano i kurziv ----------

        private void Podebljano_Click(object sender, EventArgs e)
        {
            tsbPodebljano.Checked = !tsbPodebljano.Checked;
            mnuPodebljano.Checked = tsbPodebljano.Checked;   // isto stanje i u meniju
            PrimeniStil();
        }

        private void Kurziv_Click(object sender, EventArgs e)
        {
            tsbKurziv.Checked = !tsbKurziv.Checked;
            mnuKurziv.Checked = tsbKurziv.Checked;
            PrimeniStil();
        }

        private void PrimeniStil()
        {
            FontStyle stil = FontStyle.Regular;
            if (tsbPodebljano.Checked)
            {
                stil = stil | FontStyle.Bold;
            }
            if (tsbKurziv.Checked)
            {
                stil = stil | FontStyle.Italic;
            }
            txtTekst.Font = new Font(txtTekst.Font.FontFamily, txtTekst.Font.Size, stil);
        }

        // ---------- Veličina slova ----------

        // Zajednička metoda za obradu događaja za svih pet stavki podmenija Veličina slova
        private void Velicina_Click(object sender, EventArgs e)
        {
            ToolStripMenuItem stavka = (ToolStripMenuItem)sender;
            PostaviVelicinu(int.Parse(stavka.Text));
        }

        private void tscVelicina_SelectedIndexChanged(object sender, EventArgs e)
        {
            PostaviVelicinu(int.Parse(tscVelicina.Text));
        }

        private void PostaviVelicinu(int velicina)
        {
            txtTekst.Font = new Font(txtTekst.Font.FontFamily, velicina, txtTekst.Font.Style);

            // Štikliramo samo stavku sa izabranom veličinom
            foreach (ToolStripMenuItem stavka in mnuVelicina.DropDownItems)
            {
                stavka.Checked = stavka.Text == velicina.ToString();
            }
            tscVelicina.SelectedItem = velicina.ToString();
        }

        // ---------- Boja slova ----------

        private void Boja_Click(object sender, EventArgs e)
        {
            ToolStripMenuItem stavka = (ToolStripMenuItem)sender;
            PostaviBoju(stavka.Text);
        }

        private void tscBoja_SelectedIndexChanged(object sender, EventArgs e)
        {
            PostaviBoju(tscBoja.Text);
        }

        private void PostaviBoju(string boja)
        {
            if (boja == "Plava")
            {
                txtTekst.ForeColor = Color.Blue;
            }
            else if (boja == "Crvena")
            {
                txtTekst.ForeColor = Color.Red;
            }
            else if (boja == "Zelena")
            {
                txtTekst.ForeColor = Color.Green;
            }
            else
            {
                txtTekst.ForeColor = Color.Black;
            }

            foreach (ToolStripMenuItem stavka in mnuBoja.DropDownItems)
            {
                stavka.Checked = stavka.Text == boja;
            }
            tscBoja.SelectedItem = boja;
        }

        // ---------- Stanje komandi i traka statusa ----------

        private void txtTekst_TextChanged(object sender, EventArgs e)
        {
            OsveziStanje();
        }

        private void OsveziStanje()
        {
            // Kopiraj sve ima smisla samo kada postoji tekst
            bool imaTeksta = txtTekst.Text != "";
            tsbKopirajSve.Enabled = imaTeksta;
            mnuKopirajSve.Enabled = imaTeksta;

            // Prelaz u novi red (\r\n) ne brojimo kao karaktere
            lblKarakteri.Text = "Karaktera: " + txtTekst.Text.Replace("\r\n", "").Length;

            string[] reci = txtTekst.Text.Split(new char[] { ' ', '\r', '\n', '\t' },
                StringSplitOptions.RemoveEmptyEntries);
            lblReci.Text = "Reči: " + reci.Length;
        }

        // ---------- Prikaz ----------

        private void mnuPaleta_CheckedChanged(object sender, EventArgs e)
        {
            toolStrip1.Visible = mnuPaleta.Checked;
        }

        private void mnuTrakaStatusa_CheckedChanged(object sender, EventArgs e)
        {
            statusStrip1.Visible = mnuTrakaStatusa.Checked;
        }

        // ---------- Pomoć ----------

        private void mnuOProgramu_Click(object sender, EventArgs e)
        {
            MessageBox.Show("Uređivač teksta, verzija 1.0\nAutor: Marko Marković, IV-2",
                "O programu", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }
    }
}
```

## Rezime

- **Paleta komandi (ToolStrip)** sadrži samo najčešće komande, koje se izvršavaju jednim klikom. Dugme (ToolStripButton) ima DisplayStyle, ToolTipText, Checked i Enabled; padajuća lista (ToolStripComboBox) ima Items i DropDownStyle.
- **Glavni meni** sadrži sve komande programa, sa natpisom, pristupnim tasterom i prečicom.
- Ista komanda iz menija i sa palete koristi **zajedničku metodu za obradu događaja** (izbor u kartici Events), a kada su događaji različiti, **zajedničku metodu**. Stanje (Checked, Enabled) se uvek menja na oba mesta.
- Parametar **sender** je kontrola koja je izazvala događaj; pretvaramo ga u stavku menija sa (ToolStripMenuItem)sender.
- **Traka statusa (StatusStrip)** prikazuje podatke o radu (broj karaktera i reči), a trake se sakrivaju svojstvom Visible.

## Pitanja za samoproveru

1. Navedite tri razlike između glavnog menija i palete komandi.
2. Zašto za podebljano na paleti postavljamo CheckOnClick = False, a stanje menjamo u kodu?
3. Kako postižete da stavka menija i dugme palete pozivaju istu metodu za obradu događaja?
4. Šta je sender i zašto pišemo (ToolStripMenuItem)sender?
5. Zašto broj karaktera bez Replace("\\r\\n", "") poraste za 2 posle svakog pritiska tastera Enter?

## Zadaci

Svim dugmadima palete postavite DisplayStyle = Text. Zadaci 3, 6 i 7 nadograđuju ranije zadatke ili Uređivač: pre izmena napravite kopiju projekta.

| Zadatak | Nivo | Kada |
| --- | --- | --- |
| 1. Kalkulator sa paletom | \[OSNOVNI\] | na času 4 ko završi Uređivač, ostali za domaći |
| 2. Muzički plejer | \[SREDNJI\] | domaći posle časa 4 |
| 3. Kalkulator sa menijem i paletom | \[OSNOVNI\] | na času 5 ko završi Uređivač, ostali za domaći |
| 4. Pametna kuća | \[SREDNJI\] | domaći posle časa 5 |
| 5. Lista zadataka sa trakom statusa | \[OSNOVNI\] | na času 6 ko završi Uređivač, ostali za domaći |
| 6. Lista zadataka sa menijem i paletom | \[SREDNJI\] | domaći posle časa 6 |
| 7. Uređivač: podvučeno i opis formata | \[NAPREDNI\] | domaći posle časa 6 |
| 8. Bogati uređivač | \[NAPREDNI\] | domaći posle časa 6 |

### Zadatak 1 \[OSNOVNI\] – Kalkulator sa paletom

Na formi su dva polja za brojeve i labela za rezultat. Na paleti su dugmad +, -, × i ÷, svako sa opisom (Saberi, Oduzmi, Pomnoži, Podeli). Klik na dugme računa i ispisuje rezultat. Ako polje ne sadrži broj, prikažite poruku o grešci; deljenje nulom prijavite upozorenjem. Savet: napišite jednu metodu Izracunaj(string operacija) koju poziva svako dugme.

### Zadatak 2 \[SREDNJI\] – Muzički plejer

U listi je pet pesama (izmislite nazive). Paleta ima dugmad Prethodna, Pusti, Stop i Sledeća. Labela pokazuje „Svira: naziv pesme“ ili „Zaustavljeno“.

- Dok muzika svira, Pusti je sivo; dok ne svira, Stop je sivo.
- Sledeća posle poslednje pesme ide na prvu, a Prethodna pre prve na poslednju.
- Ako muzika svira, klik na pesmu u listi odmah menja natpis.

### Zadatak 3 \[OSNOVNI\] – Kalkulator sa menijem i paletom

Kalkulatoru iz Zadatka 1 dodajte meni Operacija sa stavkama Saberi (Ctrl+1), Oduzmi (Ctrl+2), Pomnoži (Ctrl+3), Podeli (Ctrl+4), separatorom i stavkom Obriši polja (Ctrl+N). Prve četiri stavke moraju koristiti postojeće metode za obradu događaja dugmadi palete, bez novog koda. Obriši polja postoji samo u meniju. Zašto?

### Zadatak 4 \[SREDNJI\] – Pametna kuća

Tri labele pokazuju stanje svetla: „Dnevna soba: isključeno“ (siva pozadina) ili „uključeno“ (žuta pozadina). Svetla se uključuju i isključuju iz menija Svetla (Dnevna soba, Kuhinja, Spavaća soba, separator, Sve isključi) i sa palete (ista četiri dugmeta).

- Kvačica u meniju i utisnuto dugme na paleti uvek pokazuju isto stanje.
- Sve isključi je sivo i u meniju i na paleti kada su sva svetla isključena.

Savet: napišite metodu PostaviSvetlo koja za jednu sobu podesi stavku menija, dugme i labelu.

### Zadatak 5 \[OSNOVNI\] – Lista zadataka sa trakom statusa

Polje za unos i dugme Dodaj dodaju zadatak u listu (prazan unos: upozorenje). U traci statusa su dve oznake: „Zadataka: 3“ i „Izabran: Kupiti hleb“ (ili „Izabran: nijedan“). Oznake se menjaju posle dodavanja i posle izbora u listi.

### Zadatak 6 \[SREDNJI\] – Lista zadataka sa menijem i paletom

Listi iz Zadatka 5 dodajte meni Zadaci sa stavkama Obriši izabrani (Ctrl+D) i Obriši sve (Ctrl+Shift+D), i paletu sa ista dva dugmeta. Meni i paleta dele metode za obradu događaja. Obriši izabrani je sivo kada ništa nije izabrano, a Obriši sve kada je lista prazna, na oba mesta. Obriši sve traži potvrdu. Zašto prečica ne sme biti taster Delete?

### Zadatak 7 \[NAPREDNI\] – Uređivač: podvučeno i opis formata

Uređivaču dodajte Podvučeno (Ctrl+U) i u meni Format i na paletu (dugme U), sa zajedničkom metodom za obradu događaja. U traku statusa dodajte oznaku koja opisuje format, npr. „Format: podebljano, kurziv, 14 pt“ ili „Format: obično, 12 pt“.

### Zadatak 8 \[NAPREDNI\] – Bogati uređivač

Napravite nov projekat sa poljem RichTextBox (prefiks rtb) umesto TextBox. Za razliku od TextBox-a, RichTextBox može da formatira samo izabrani deo teksta. Podebljano i Kurziv, iz menija i sa palete, menjaju samo izabrani tekst (svojstvo SelectionFont). Kada se kursor pomeri, dugmad i stavke menija pokazuju format teksta na tom mestu (događaj SelectionChanged).
