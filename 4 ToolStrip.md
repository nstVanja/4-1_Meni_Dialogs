# Lekcija: Traka sa sličicama i tekstom

Programiranje, IV razred · Visual C# 2010 Express · Paleta komandi (ToolStrip) sa velikim ikonama

## Šta pravimo

Traka sa velikim sličicama i tekstom ispod njih je paleta komandi (ToolStrip) kojoj su promenjena tri svojstva; kod za to nije potreban.

Pravimo aplikaciju **Planete**. Na traci su dugmad Zemlja, Mars, Jupiter, O aplikaciji i Izlaz. Klik na planetu prikazuje njenu sliku, naziv i kratak opis.

| Deo forme | Kontrola | Šta prikazuje |
| --- | --- | --- |
| traka na vrhu | ToolStrip sa pet dugmadi | sličica iznad teksta |
| levo | PictureBox | veliku sliku izabrane planete |
| desno | dva Label-a | naziv i opis planete |

## Priprema slika

Od nastavnika preuzmite fasciklu sa pet slika i kopirajte je na radnu površinu:

| Datoteka | Gde se koristi |
| --- | --- |
| zemlja.jpg, mars.jpg, jupiter.jpg | sličica na dugmetu i velika slika |
| info.png, izlaz.png | samo sličica na dugmetu |

Imena datoteka su malim slovima i bez č, ć, š, ž i razmaka, jer od njih nastaju imena u kodu.

Iste fotografije koristimo i za dugme i za veliku sliku: traka ih sama smanji na 48 × 48 piksela.

## Korak 1 – Forma i kontrole za sadržaj

Napravite novi projekat Windows Forms Application pod imenom **Planete**.

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | Form1 | Text: Planete Sunčevog sistema, Size: 760; 480, StartPosition: CenterScreen |
| PictureBox | picSlika | Location: 20; 110, Size: 360; 300, SizeMode: Zoom, BorderStyle: FixedSingle |
| Label | lblNaziv | Location: 400; 110, Font: Segoe UI; 16; Bold, Text: Dobro došli! |
| Label | lblOpis | Location: 400; 160, AutoSize: False, Size: 320; 240, Font: Segoe UI; 11, Text: Izaberite planetu na traci. |

AutoSize: False na lblOpis je važno: tada se dug tekst prelama u više redova unutar zadate veličine, umesto da ode van forme.

## Korak 2 – Traka sa sličicama

Iz Toolbox-a (grupa Menus & Toolbars) prevucite **ToolStrip** na formu. Traka se sama zalepi za vrh forme.

**Svojstvo cele trake (izaberite toolStrip1):**

| Svojstvo | Vrednost | Šta postiže |
| --- | --- | --- |
| ImageScalingSize | 48; 48 | sve sličice na traci su velike 48 × 48 (podrazumevano je 16 × 16) |

Preko strelice na traci dodajte pet dugmadi (**Button**) i svako podesite:

| Name | Text | ToolTipText |
| --- | --- | --- |
| tsbZemlja | Zemlja | Prikaži Zemlju |
| tsbMars | Mars | Prikaži Mars |
| tsbJupiter | Jupiter | Prikaži Jupiter |
| tsbOAplikaciji | O aplikaciji | Podaci o aplikaciji |
| tsbIzlaz | Izlaz | Zatvori aplikaciju |

**Svojstva koja su ista za svih pet dugmadi.** Izaberite prvo dugme, pa sa pritisnutim Ctrl kliknite na ostala četiri: svojstvo koje tada promenite menja se svima odjednom.

| Svojstvo | Vrednost | Šta postiže |
| --- | --- | --- |
| DisplayStyle | ImageAndText | prikazuje i sličicu i tekst |
| TextImageRelation | ImageAboveText | sličica je iznad teksta |
| ImageScaling | SizeToFit | sličica se smanji na veličinu iz ImageScalingSize |

**Sličica na dugmetu.** Izaberite tsbZemlja i kliknite na tri tačke pored svojstva **Image**. U prozoru Select Resource:

1. izaberite **Project resource file** (ne Local resource);
2. kliknite **Import...** i izaberite **svih pet** slika odjednom (Ctrl + klik);
3. sa leve strane izaberite **zemlja** i kliknite OK.

Za ostala dugmad u istom prozoru samo izaberete već uvezenu sliku: mars, jupiter, info, izlaz.

Slike su sada deo projekta (resursi). U Solution Explorer-u se vide u fascikli **Resources**, a u kodu su dostupne kao `Properties.Resources.zemlja`, `Properties.Resources.mars`...

**Podsetnik: tri koraka do ovakve trake**

1. traka: ImageScalingSize = 48; 48;
2. dugmad: DisplayStyle = ImageAndText, TextImageRelation = ImageAboveText;
3. svako dugme: Image iz Project resource file.

## Korak 3 – Kod

Sva tri dugmeta za planete rade isto: postave sliku, naziv i opis. Zato taj posao pišemo jednom, u zajedničkoj metodi PrikaziPlanetu, a metode za obradu događaja je samo pozivaju sa različitim podacima.

Otvorite kod forme (F7) i u klasu Form1, ispod konstruktora, dodajte metodu:

```csharp
// Prikazuje sliku, naziv i opis izabrane planete
private void PrikaziPlanetu(Image slika, string naziv, string opis)
{
    picSlika.Image = slika;
    lblNaziv.Text = naziv;
    lblOpis.Text = opis;
}
```

Zatim dvoklikom na svako dugme na traci napravite metode za obradu događaja:

```csharp
private void tsbZemlja_Click(object sender, EventArgs e)
{
    PrikaziPlanetu(Properties.Resources.zemlja, "Zemlja",
        "Zemlja je treća planeta od Sunca i jedina za koju znamo " +
        "da na njoj postoji život. Oko Sunca obiđe za 365 dana.");
}

private void tsbMars_Click(object sender, EventArgs e)
{
    PrikaziPlanetu(Properties.Resources.mars, "Mars",
        "Mars zovu Crvena planeta, jer mu je tlo puno rđe. " +
        "Ima dva mala meseca, Fobos i Deimos.");
}

private void tsbJupiter_Click(object sender, EventArgs e)
{
    PrikaziPlanetu(Properties.Resources.jupiter, "Jupiter",
        "Jupiter je najveća planeta Sunčevog sistema. " +
        "U njega bi stalo više od 1.300 planeta veličine Zemlje.");
}
```

```csharp
private void tsbOAplikaciji_Click(object sender, EventArgs e)
{
    MessageBox.Show("Planete, verzija 1.0\nAutor: Marko Marković, IV-2",
        "O aplikaciji", MessageBoxButtons.OK, MessageBoxIcon.Information);
}

private void tsbIzlaz_Click(object sender, EventArgs e)
{
    this.Close();
}
```

### Objašnjenje

| Deo koda | Značenje |
| --- | --- |
| Image slika | parametar tipa Image: metoda prima sliku |
| Properties.Resources.zemlja | slika zemlja.jpg koju smo uvezli u resurse projekta |
| picSlika.Image = slika; | prikazuje sliku u kontroli PictureBox |
| "..." + "..." | dug tekst je podeljen u dva reda koda; znak + ih spaja u jedan tekst |

U poruci O aplikaciji upišite svoje ime i odeljenje.

## Korak 4 – Isprobajte

1. Pokrenite program (F5). Na traci su velike sličice sa tekstom ispod.
2. Pređite mišem preko dugmeta: pojavi se opis iz ToolTipText.
3. Kliknite redom Zemlja, Mars, Jupiter: menjaju se slika, naziv i opis.
4. O aplikaciji prikazuje poruku, Izlaz zatvara program.

## Česte greške

| Šta vidite | Uzrok | Rešenje |
| --- | --- | --- |
| Sličice su male | ImageScalingSize je promenjen na dugmetu, a ne na traci; ili ImageScaling nije SizeToFit | izaberite toolStrip1 i postavite ImageScalingSize: 48; 48 |
| Tekst je pored sličice, a ne ispod | TextImageRelation nije ImageAboveText | postavite ga na svih pet dugmadi |
| Vidi se samo sličica ili samo tekst | DisplayStyle je Image ili Text | postavite ImageAndText |
| Properties.Resources.zemlja je podvučeno crveno | slika je uvezena kao Local resource, ili se resurs zove drugačije (npr. Zemlja, velikim slovom) | ponovo uvezite sliku preko Project resource file; ime proverite u fascikli Resources |
| Velika slika je razvučena ili isečena | SizeMode nije Zoom | picSlika: SizeMode = Zoom |
| Opis je u jednom redu i izlazi van forme | lblOpis ima AutoSize: True | postavite AutoSize: False i Size: 320; 240 |
| Slika i tekst su delom ispod trake | kontrole su postavljene previsoko | pomerite ih niže (Location Y oko 110) |

## Rezime

- Traka sa velikim sličicama je obična paleta komandi (ToolStrip).
- Veličina sličica se podešava na traci (ImageScalingSize), a izgled dugmeta na dugmetu (DisplayStyle, TextImageRelation).
- Slike se uvoze u resurse projekta (Project resource file) i u kodu se koriste kao `Properties.Resources.ime`.
- Kada više dugmadi radi isti posao sa različitim podacima, posao pišemo jednom, u zajedničkoj metodi sa parametrima.

## Pitanja za proveru

1. Koje svojstvo određuje veličinu sličica i na kojoj kontroli se podešava?
2. Koja dva svojstva dugmeta postavljaju sličicu iznad teksta?
3. Zašto postoji metoda PrikaziPlanetu i koja su njena tri parametra?
4. Koja je razlika između ove trake i glavnog menija (MenuStrip)?

## Zadatak za vežbu

1. Dodajte četvrto dugme **Saturn** (tsbSaturn) sa slikom saturn.jpg i kratkim opisom, na primer: „Saturn je poznat po prstenovima od leda i kamena.“ Postavite ga ispred dugmeta O aplikaciji i dodajte separator između planeta i ostalih dugmadi.
2. Napravite sličnu aplikaciju na drugu temu, na primer **Moj grad** (tri znamenitosti) ili **Životinje** (tri životinje): traka sa sličicama i tekstom, velika slika i opis.

*Za višu ocenu:* iznad trake dodajte glavni meni (MenuStrip) sa menijem Planete i stavkama Zemlja, Mars, Jupiter. Stavke menija imaju male sličice (svojstvo Image) i koriste iste metode za obradu događaja kao dugmad na traci, bez novog koda.
