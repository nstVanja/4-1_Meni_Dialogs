# Rad sa grafikom u C# – materijal za učenike

Programiranje, 4. razred · Nov 23, 2026 · @Vanja

## Uvod

Naučićete da crtate oblike i tekst po formi i kontrolama, da crtež ostane i posle osvežavanja forme i da ga pokrećete mišem i tajmerom.

**Blok 1 (časovi 1–3) – posle njega znate da:**

- objasnite koordinatni sistem forme (tačka (0, 0) je gore levo);
- napravite objekte klasa Graphics, Pen, SolidBrush, Color i Point;
- nacrtate liniju, pravougaonik, kvadrat, elipsu, krug, luk i tekst, prazne i popunjene.

**Blok 2 (časovi 4–6) – posle njega znate da:**

- objasnite zašto crtež nestaje i crtate u metodi za obradu događaja Paint;
- koristite metode Invalidate i Refresh;
- koristite klasu Random i koordinate klika mišem;
- crtate mišem i napravite jednostavnu animaciju pomoću komponente Timer.

**Potrebno:** Visual C# 2010 Express, projekat tipa *Windows Forms Application*. Svi primeri rade u toj verziji. Na vrhu fajla Form1.cs već postoje `using System.Drawing;`, `using System.Windows.Forms;` i `using System.Collections.Generic;`, pa ih ne dodajete.

## 1. Koordinatni sistem i površina za crtanje

Svaka forma i kontrola ima svoj koordinatni sistem: početak (0, 0) je u **gornjem levom uglu**, x raste udesno, a **y raste naniže**. Jedinica je piksel.

| Pojam | Značenje |
| --- | --- |
| x | rastojanje od leve ivice, u pikselima |
| y | rastojanje od gornje ivice, u pikselima (raste naniže, ne naviše kao u matematici) |
| ClientSize.Width, ClientSize.Height | širina i visina unutrašnjeg dela forme, bez naslovne linije i okvira |
| Width, Height | širina i visina cele forme ili kontrole, sa okvirom |

Primer: tačka (100, 50) je 100 piksela desno i 50 piksela ispod gornjeg levog ugla.

Crtati možemo po formi (`this`) ili po kontroli, najčešće po kontroli **Panel** ili **PictureBox**. Svaka kontrola ima svoj koordinatni sistem: (0, 0) panela je gornji levi ugao panela, a ne forme. Zato je zgodno crtati po panelu belog BackColor-a: crtež ne prelazi preko dugmadi.

## 2. Osnovne klase za crtanje

Za svaki crtež potrebni su površina (**Graphics**) i alat: olovka (**Pen**) za ivice ili četka (**Brush**) za popunjavanje. Sve ove klase nalaze se u imenskom prostoru System.Drawing.

| Klasa | Uloga | Primer |
| --- | --- | --- |
| Graphics | površina po kojoj crtamo; ima metode Draw… i Fill… | `Graphics g = pnlCrtez.CreateGraphics();` |
| Pen | olovka: boja, debljina i stil linije | `Pen olovka = new Pen(Color.Red, 3);` |
| SolidBrush | četka jedne boje, za popunjene oblike i tekst | `SolidBrush cetka = new SolidBrush(Color.Yellow);` |
| Color | boja | `Color.Blue`, `Color.FromArgb(255, 128, 0)` |
| Point, PointF | tačka sa celim, odnosno realnim koordinatama | `Point p = new Point(10, 20);` |
| Font | font za tekst | `Font f = new Font("Arial", 16, FontStyle.Bold);` |

### Graphics

Objekat klase Graphics ne pravimo sa `new`, nego ga dobijamo od kontrole po kojoj crtamo:

- `this.CreateGraphics()` ili `pnlCrtez.CreateGraphics()` – crtamo odmah, na primer na klik dugmeta (blok 1);
- `e.Graphics` u metodi za obradu događaja Paint (blok 2).

### Pen – olovka

```csharp
Pen olovka = new Pen(Color.Red, 5);    // crvena, debljina 5 piksela
olovka.Width = 2;                       // promena debljine
olovka.Color = Color.Green;             // promena boje
olovka.DashStyle = System.Drawing.Drawing2D.DashStyle.Dash;  // isprekidana linija
```

Stilovi linije su Solid (puna, podrazumevana), Dash, Dot, DashDot i DashDotDot. Nalaze se u imenskom prostoru System.Drawing.Drawing2D; ako na vrh fajla dodate `using System.Drawing.Drawing2D;`, dovoljno je pisati `DashStyle.Dash`.

Gotove olovke debljine 1, koje ne pravimo sa `new`: `Pens.Black`, `Pens.Red`, `Pens.Blue`…

### Brush – četka

Brush je opšta (apstraktna) klasa četke, a SolidBrush je četka jedne boje. Četkom popunjavamo oblike (metode Fill…) i crtamo tekst (DrawString).

```csharp
SolidBrush cetka = new SolidBrush(Color.LightBlue);
```

Gotove četke: `Brushes.Yellow`, `Brushes.Black`…

### Color – boja

```csharp
Color c1 = Color.Green;                    // imenovana boja
Color c2 = Color.FromArgb(255, 128, 0);    // crvena 255, zelena 128, plava 0 = narandžasta
Color c3 = Color.FromArgb(128, 255, 0, 0); // prvi broj je providnost: poluprovidna crvena
```

Komponente boje su celi brojevi od 0 do 255: (0, 0, 0) je crna, (255, 255, 255) bela.

### Point i PointF – tačka

```csharp
Point a = new Point(10, 20);           // cele koordinate
PointF b = new PointF(10.5f, 20.25f);  // realne koordinate – obavezan sufiks f
```

Bez sufiksa `f` kompajler javlja grešku, jer je `10.5` tipa double, a PointF očekuje float.

## 3. Crtanje oblika i teksta

Svaki oblik ima svoju metodu klase Graphics: **Draw…** crta samo ivicu olovkom (Pen), a **Fill…** popunjava unutrašnjost četkom (Brush).

| Metoda | Šta crta | Parametri |
| --- | --- | --- |
| DrawLine | liniju | olovka, x1, y1, x2, y2 (ili olovka i dve tačke) |
| DrawRectangle | pravougaonik; kvadrat ako je širina = visina | olovka, x, y, širina, visina |
| DrawEllipse | elipsu; krug ako je širina = visina | olovka, x, y, širina, visina opisanog pravougaonika |
| DrawArc | luk elipse | olovka, x, y, širina, visina, početni ugao, ugao luka |
| DrawString | tekst | tekst, font, četka, x, y |
| FillRectangle | popunjen pravougaonik | četka, x, y, širina, visina |
| FillEllipse | popunjenu elipsu ili krug | četka, x, y, širina, visina |
| FillPie | popunjen isečak („parče pice“) | četka, x, y, širina, visina, početni ugao, ugao |
| Clear | briše celu površinu jednom bojom | boja |

### Priprema projekta

1. Napravite novi projekat tipa Windows Forms Application (na primer GrafikaOsnove).
2. Na formu postavite Panel: Name = pnlCrtez, Size = 400; 300, BackColor = White.
3. Pored panela postavite dugmad: btnLinija, btnPravougaonik, btnKvadrat, btnElipsa, btnKrug, btnLuk, btnTekst i btnObrisi.
4. Dvoklikom na dugme Visual Studio pravi metodu za obradu događaja Click; u nju upisujete kod.

### 3.1 Linija

```csharp
private void btnLinija_Click(object sender, EventArgs e)
{
    Graphics g = pnlCrtez.CreateGraphics();
    Pen olovka = new Pen(Color.Black, 2);
    g.DrawLine(olovka, 20, 20, 380, 20);   // od (20, 20) do (380, 20)
    olovka.Dispose();                      // oslobađanje resursa
    g.Dispose();
}
```

Objekti Graphics, Pen, SolidBrush i Font zauzimaju resurse sistema, pa ih posle crtanja oslobađamo metodom `Dispose()`. Kraći i sigurniji način je naredba `using`: ona sama poziva Dispose na kraju bloka. U ostalim primerima koristimo `using`.

```csharp
private void btnLinija_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Blue, 2))
    {
        g.DrawLine(olovka, 20, 20, 380, 20);
        g.DrawLine(olovka, new Point(20, 40), new Point(380, 280));  // preko tačaka
    }
}
```

### 3.2 Pravougaonik i kvadrat

`DrawRectangle(olovka, x, y, širina, visina)` – (x, y) je gornji levi ugao. Kvadrat je pravougaonik jednake širine i visine.

```csharp
private void btnPravougaonik_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Green, 3))
    {
        g.DrawRectangle(olovka, 50, 50, 200, 100);   // 200 x 100 piksela
    }
}

private void btnKvadrat_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Red, 3))
    {
        int x = 270, y = 50, strana = 100;
        g.DrawRectangle(olovka, x, y, strana, strana);
    }
}
```

### 3.3 Elipsa i krug

Elipsa se crta unutar zamišljenog, opisanog pravougaonika: zadajemo njegov gornji levi ugao, širinu i visinu, **a ne centar** elipse.

```csharp
private void btnElipsa_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Purple, 2))
    {
        g.DrawEllipse(olovka, 50, 170, 150, 100);
    }
}
```

Kada znamo centar (cx, cy) i poluprečnik r, gornji levi ugao je (cx − r, cy − r), a širina i visina su 2r:

```csharp
private void btnKrug_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Red, 2))
    {
        int cx = 300, cy = 220, r = 50;    // centar i poluprečnik
        g.DrawEllipse(olovka, cx - r, cy - r, 2 * r, 2 * r);
    }
}
```

### 3.4 Kružni luk

`DrawArc(olovka, x, y, širina, visina, početniUgao, ugaoLuka)` crta deo elipse. Uglovi su u stepenima: 0° je desno („3 sata“), a pozitivan ugao ide **u smeru kazaljke na satu**, jer y raste naniže. Zato je 90° dole, 180° levo, a 270° gore.

```csharp
private void btnLuk_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Pen olovka = new Pen(Color.Orange, 3))
    {
        g.DrawArc(olovka, 100, 50, 200, 200, 0, 180);   // donja polovina kruga – osmeh
    }
}
```

Još primera: (0, 90) je donja desna četvrtina, (180, 180) gornja polovina, (0, 360) ceo krug.

### 3.5 Tekst (baner)

Tekst se crta četkom, a ne olovkom. (x, y) je gornji levi ugao teksta; veličina fonta zadaje se u tačkama (pt), kao u Wordu.

```csharp
private void btnTekst_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    using (Font font = new Font("Arial", 20, FontStyle.Bold))
    using (SolidBrush cetka = new SolidBrush(Color.DarkBlue))
    {
        g.DrawString("Dobrodošli!", font, cetka, 120, 10);
    }
}
```

### 3.6 Popunjeni oblici

```csharp
using (Graphics g = pnlCrtez.CreateGraphics())
using (SolidBrush cetka = new SolidBrush(Color.Yellow))
{
    g.FillRectangle(cetka, 50, 50, 200, 100);        // prvo popuna
    g.DrawRectangle(Pens.Black, 50, 50, 200, 100);   // pa ivica preko nje
    g.FillEllipse(Brushes.LightBlue, 270, 50, 100, 100);
}
```

Redosled je važan: ono što se nacrta kasnije prekriva ono što je nacrtano ranije. Zato prvo Fill, pa Draw za ivicu.

### 3.7 Brisanje

```csharp
private void btnObrisi_Click(object sender, EventArgs e)
{
    using (Graphics g = pnlCrtez.CreateGraphics())
    {
        g.Clear(Color.White);   // cela površina postaje bela
    }
}
```

## Zadaci – blok 1

Svaki zadatak radite kao posebno dugme u projektu GrafikaOsnove i crtate po panelu pnlCrtez (400 × 300). Uradite sve zadatke osnovnog nivoa, pa nastavite redom.

### Osnovni nivo

1. **Kućica.** Nacrtajte kućicu: kvadrat za zidove, dve linije za krov i pravougaonik za vrata. Debljina olovke je 2.
2. **Semafor.** Nacrtajte crni popunjen pravougaonik i u njemu tri popunjena kruga jedan ispod drugog: crveni, žuti i zeleni.
3. **Moje ime.** Ispišite svoje ime fontom Arial 24, podebljano, u boji `Color.FromArgb(0, 102, 204)`, i podvucite ga linijom iste boje.

### Srednji nivo

4. **Meta.** Pomoću petlje for nacrtajte 5 krugova sa istim centrom (200, 150) i poluprečnicima 20, 40, 60, 80 i 100.
   - Dodatak: krugovi su naizmenično popunjeni crveno i belo. Pomoć: crtajte od najvećeg ka najmanjem.
5. **Mreža.** Nacrtajte mrežu horizontalnih i vertikalnih linija preko celog panela, na razmaku od 20 piksela. Koristite petlje i `pnlCrtez.Width`, `pnlCrtez.Height`.
6. **Smajli.** Nacrtajte žuti popunjen krug sa crnom ivicom, dva crna oka (popunjeni krugovi) i osmeh (DrawArc).

### Napredni nivo

7. **Šahovska tabla.** Nacrtajte tablu 8 × 8 polja, stranice polja 30 piksela, naizmenično crnih i belih, sa okvirom oko cele table. Pomoć: polje u redu i i koloni j je crno ako je `(i + j) % 2 == 1`.
8. **Elipsa po meri.** Dodajte TextBox kontrole txtX, txtY, txtSirina i txtVisina i dugme „Nacrtaj“. Na klik nacrtajte elipsu sa unetim vrednostima. Ako unos nije broj ili elipsa izlazi iz panela, prikažite poruku u MessageBox-u. Pomoć: `int.TryParse`.
9. **Stubičasti dijagram.** Niz `int[] broj = { 2, 5, 8, 6, 3 };` sadrži broj učenika sa ocenama 1 do 5. Nacrtajte 5 popunjenih stubića širine 40 i visine `broj[i] * 25` piksela, sa razmakom 20, poravnatih na dno panela, i ispod svakog ispišite ocenu.

## 4. Zašto crtež nestaje? Događaj Paint

Crtež napravljen preko CreateGraphics nestaje kada se forma minimizuje, kada se preko nje prevuče drugi prozor ili kada joj se promeni veličina. Windows ne pamti šta smo nacrtali: kada je potrebno, on ponovo iscrta formu i kontrole i pritom poziva događaj **Paint**. Zato crtež koji treba da ostane pišemo u metodi za obradu događaja Paint.

**Probajte:** nacrtajte kućicu dugmetom iz bloka 1, minimizujte formu i vratite je.

### Kako se pravi metoda za obradu događaja Paint

1. U dizajneru izaberite formu (ili panel po kom crtate).
2. U prozoru Properties kliknite na ikonu munje (Events).
3. Dvoklik na događaj Paint.

```csharp
private void Form1_Paint(object sender, PaintEventArgs e)
{
    Graphics g = e.Graphics;            // površinu dobijamo gotovu
    using (Pen olovka = new Pen(Color.Blue, 3))
    {
        g.DrawRectangle(olovka, 50, 50, 200, 100);
    }
    g.DrawString("Ja ne nestajem!", Font, Brushes.Black, 60, 160);
}
```

- Za `e.Graphics` ne pozivamo Dispose – to radi sistem.
- Paint se poziva često i automatski, zato u njemu samo crtamo: bez MessageBox-a i bez menjanja podataka.
- Metodu Paint ne pozivamo sami, nego tražimo novo iscrtavanje.

### Invalidate i Refresh

| Metoda | Šta radi |
| --- | --- |
| Invalidate() | označava kontrolu za ponovno iscrtavanje; Paint se poziva čim sistem stigne (preporučeno) |
| Refresh() | odmah briše i ponovo iscrtava kontrolu |

Za formu pišemo `Invalidate();`, a za panel `pnlCrtez.Invalidate();`.

### Crtež koji zavisi od podataka

Podatke koje crtež koristi čuvamo u **poljima klase** (promenljive izvan metoda), jer se one pamte između događaja. Dugme menja podatke i poziva Invalidate, a Paint crta na osnovu podataka.

```csharp
public partial class Form1 : Form
{
    int poluprecnik = 20;   // polje klase

    public Form1()
    {
        InitializeComponent();
        ResizeRedraw = true;   // iscrtaj ponovo i kad se menja veličina forme
    }

    private void btnVeci_Click(object sender, EventArgs e)
    {
        poluprecnik += 10;
        Invalidate();          // traži ponovno iscrtavanje forme
    }

    private void Form1_Paint(object sender, PaintEventArgs e)
    {
        int cx = ClientSize.Width / 2;
        int cy = ClientSize.Height / 2;
        e.Graphics.FillEllipse(Brushes.Orange, cx - poluprecnik, cy - poluprecnik,
                               2 * poluprecnik, 2 * poluprecnik);
    }
}
```

Krug je uvek u centru forme, ostaje posle minimizacije i raste na svaki klik.

## 5. Slučajni brojevi i klik mišem

### Klasa Random

```csharp
Random R = new Random();
int a = R.Next();          // nenegativan ceo broj
int b = R.Next(256);       // od 0 do 255
int c = R.Next(10, 21);    // od 10 do 20 – gornja granica se ne uključuje
```

Objekat Random pravite **jednom, kao polje klase**. Ako ga pravite u svakom kliku, dva brza klika mogu dati iste brojeve, jer se generator pokreće prema trenutnom vremenu.

### Događaj MouseClick

Kada korisnik klikne mišem na formu, poziva se metoda za obradu događaja MouseClick (napravite je dvoklikom na MouseClick u Properties → Events). Parametar `e` je tipa MouseEventArgs i nosi podatke o kliku:

| Svojstvo | Značenje |
| --- | --- |
| e.X, e.Y | koordinate klika u odnosu na kontrolu na koju je kliknuto |
| e.Location | ista tačka, kao Point |
| e.Button | dugme miša: MouseButtons.Left, MouseButtons.Right ili MouseButtons.Middle |

### Primer 1: slučajna boja pozadine

Klik na formu menja boju pozadine, a u naslovnoj liniji ispisuje koordinate klika.

```csharp
public partial class Form1 : Form
{
    Random R = new Random();   // jedan generator za celu formu

    public Form1()
    {
        InitializeComponent();
    }

    private void Form1_Load(object sender, EventArgs e)
    {
        Text = "Klikni na formu";
        Width = 500;
        Height = 500;
    }

    private void Form1_MouseClick(object sender, MouseEventArgs e)
    {
        BackColor = Color.FromArgb(R.Next(256), R.Next(256), R.Next(256));
        Text = "Kliknuli ste na tačku (" + e.X + ", " + e.Y + ")";
    }
}
```

### Primer 2: krug na mestu klika

Svaki levi klik dodaje tačku u listu, desni klik briše sve. Paint crta krug oko svake tačke iz liste, pa krugovi ostaju i posle minimizacije.

```csharp
List<Point> tacke = new List<Point>();   // polje klase

private void Form1_MouseClick(object sender, MouseEventArgs e)
{
    if (e.Button == MouseButtons.Left)
        tacke.Add(e.Location);
    else if (e.Button == MouseButtons.Right)
        tacke.Clear();
    Invalidate();
}

private void Form1_Paint(object sender, PaintEventArgs e)
{
    int r = 10;
    foreach (Point p in tacke)
    {
        e.Graphics.FillEllipse(Brushes.Red, p.X - r, p.Y - r, 2 * r, 2 * r);
    }
}
```

## 6. Crtanje mišem

Crtanje slobodnom rukom koristi tri događaja miša. Potez se crta kao niz kratkih linija od prethodnog do trenutnog položaja miša.

| Događaj | Kada se dešava | Šta radimo |
| --- | --- | --- |
| MouseDown | korisnik pritisne dugme miša | zapamtimo početnu tačku |
| MouseMove | miš se pomera (i sa pritisnutim dugmetom i bez njega) | ako je levo dugme pritisnuto, crtamo liniju do trenutne tačke |
| MouseUp | korisnik pusti dugme | po potrebi završimo potez |

### Verzija 1 – jednostavna (CreateGraphics)

```csharp
Point prethodna;   // polje klase

private void Form1_MouseDown(object sender, MouseEventArgs e)
{
    prethodna = e.Location;
}

private void Form1_MouseMove(object sender, MouseEventArgs e)
{
    if (e.Button == MouseButtons.Left)
    {
        using (Graphics g = CreateGraphics())
        using (Pen olovka = new Pen(Color.Black, 3))
        {
            g.DrawLine(olovka, prethodna, e.Location);
        }
        prethodna = e.Location;
    }
}
```

Radi, ali crtež nestaje kada se forma minimizuje – isti problem kao u bloku 1.

### Verzija 2 – crtež koji ostaje (lista + Paint)

Svaki delić linije (segment) pamtimo kao niz od dve tačke, u listi. MouseMove dodaje segment i poziva Invalidate, a Paint crta sve segmente iz liste.

```csharp
public partial class Form1 : Form
{
    List<Point[]> segmenti = new List<Point[]>();   // svaki segment: { početak, kraj }
    Point prethodna;

    public Form1()
    {
        InitializeComponent();
        DoubleBuffered = true;   // sprečava treperenje
    }

    private void Form1_MouseDown(object sender, MouseEventArgs e)
    {
        prethodna = e.Location;
    }

    private void Form1_MouseMove(object sender, MouseEventArgs e)
    {
        if (e.Button == MouseButtons.Left)
        {
            segmenti.Add(new Point[] { prethodna, e.Location });
            prethodna = e.Location;
            Invalidate();
        }
    }

    private void Form1_Paint(object sender, PaintEventArgs e)
    {
        using (Pen olovka = new Pen(Color.Black, 3))
        {
            foreach (Point[] s in segmenti)
            {
                e.Graphics.DrawLine(olovka, s[0], s[1]);
            }
        }
    }

    private void btnObrisi_Click(object sender, EventArgs e)
    {
        segmenti.Clear();
        Invalidate();
    }
}
```

**DoubleBuffered = true** znači da se forma prvo iscrta u memoriji, pa se prikaže gotova slika, zato nema treperenja. Svojstvo možete postaviti i u prozoru Properties forme.

## 7. Animacija pomoću Timer-a

Animacija je niz slika koje se brzo smenjuju. Komponenta **Timer** na svakih Interval milisekundi poziva događaj **Tick**. U metodi za obradu događaja Tick menjamo položaj (podatke) i pozivamo Invalidate, a Paint crta sliku za taj trenutak.

### Podešavanje

1. Iz Toolbox-a (grupa Components) prevucite Timer na formu; pojaviće se ispod forme kao timer1.
2. Postavite svojstvo Interval = 30 (milisekundi, oko 33 slike u sekundi).
3. Dvoklik na timer1 pravi metodu za obradu događaja Tick.
4. Timer pokrećete sa `timer1.Start();`, a zaustavljate sa `timer1.Stop();`.
5. Za formu postavite DoubleBuffered = True, da slika ne treperi.

U Tick metodi **ne crtamo direktno**: samo menjamo podatke i pozivamo Invalidate.

### Primer 1: baner koji klizi

```csharp
public partial class Form1 : Form
{
    int x = 0;   // trenutni položaj teksta
    string poruka = "Dobrodošli na čas programiranja!";
    Font font = new Font("Arial", 24, FontStyle.Bold);

    public Form1()
    {
        InitializeComponent();
        DoubleBuffered = true;
    }

    private void btnStart_Click(object sender, EventArgs e)
    {
        timer1.Start();
    }

    private void btnStop_Click(object sender, EventArgs e)
    {
        timer1.Stop();
    }

    private void timer1_Tick(object sender, EventArgs e)
    {
        x += 5;                                   // pomeri tekst udesno
        if (x > ClientSize.Width)                 // izašao je desno?
        {
            int sirinaTeksta = TextRenderer.MeasureText(poruka, font).Width;
            x = -sirinaTeksta;                    // vrati ga levo, iza ivice
        }
        Invalidate();
    }

    private void Form1_Paint(object sender, PaintEventArgs e)
    {
        e.Graphics.DrawString(poruka, font, Brushes.DarkBlue, x, 100);
    }
}
```

`TextRenderer.MeasureText` vraća veličinu teksta u pikselima, pa tekst ponovo ulazi s leve strane taman kada potpuno izađe desno.

### Primer 2: loptica koja se odbija

```csharp
int x = 50, y = 50;   // položaj loptice (gornji levi ugao)
int dx = 4, dy = 3;   // pomeraj za jedan tik
int d = 30;           // prečnik

private void Form1_Load(object sender, EventArgs e)
{
    timer1.Start();
}

private void timer1_Tick(object sender, EventArgs e)
{
    x += dx;
    y += dy;
    if (x < 0 || x + d > ClientSize.Width) dx = -dx;    // leva ili desna ivica
    if (y < 0 || y + d > ClientSize.Height) dy = -dy;   // gornja ili donja ivica
    Invalidate();
}

private void Form1_Paint(object sender, PaintEventArgs e)
{
    e.Graphics.FillEllipse(Brushes.Red, x, y, d, d);
}
```

Promenom znaka pomeraja (dx = −dx) loptica menja smer kada udari u ivicu.

## Zadaci – blok 2

U svim zadacima crtež mora ostati i posle minimizacije forme: crtajte u metodi za obradu događaja Paint, a podatke menjajte u drugim događajima i pozovite Invalidate.

### Osnovni nivo

1. **Trajna kućica.** Kućicu iz bloka 1 premestite u metodu Paint forme. Proverite minimizacijom da kućica ostaje.
2. **Kvadrat raste.** Dugmad „Veći“ i „Manji“ menjaju stranicu kvadrata za 10 piksela, najmanje 10, a najviše 200. Kvadrat je uvek u centru forme.
3. **Klik – tačka.** Svaki levi klik crta popunjen krug poluprečnika 8 na mestu klika, u slučajnoj boji; u naslovnoj liniji piše broj krugova. Pomoć: dve liste, `List<Point>` i `List<Color>`.

### Srednji nivo

4. **Linija iz dva klika.** Prvi klik pamti početnu tačku, drugi crta liniju do nje. Sve linije ostaju na formi, a desni klik briše sve.
5. **Bojanka.** Proširite crtanje mišem: tri dugmeta (crvena, plava, crna) menjaju boju olovke za nove poteze, a NumericUpDown menja debljinu od 1 do 10. Već nacrtani potezi zadržavaju svoju boju i debljinu. Pomoć: uz svaki segment pamtite i boju i debljinu (paralelne liste).
6. **Semafor koji radi.** Timer sa Interval = 1000 menja svetlo: crveno 3 sekunde, zeleno 3 sekunde, žuto 1 sekundu, pa ponovo crveno. Pomoć: brojač sekundi od 0 do 6.

### Napredni nivo

7. **Loptica sa bojama.** Lopticu iz primera proširite: pri svakom udaru u ivicu dobija slučajnu boju, a klik na lopticu je zaustavlja ili ponovo pokreće. Pomoć: klik je unutar kruga ako je (e.X − cx)² + (e.Y − cy)² ≤ r².
8. **Analogni sat.** Nacrtajte sat koji pokazuje trenutno vreme (`DateTime.Now`): brojčanik, 12 crtica i kazaljke za sate, minute i sekunde; Timer sa Interval = 1000 ga osvežava. Pomoć: kraj kazaljke dužine L za ugao α (0° = 12 sati) je x = cx + L·sin α, y = cy − L·cos α; Math.Sin i Math.Cos traže radijane, α · Math.PI / 180.
9. **Igra „Uhvati krug“.** Krug se na svakih 800 ms pojavljuje na slučajnom mestu. Pogodak klikom donosi poen (prikaz u naslovnoj liniji); posle 30 sekundi igra se završava i ispisuje rezultat.

## Česte greške i literatura

| Problem | Uzrok | Rešenje |
| --- | --- | --- |
| Crtež nestaje posle minimizacije | crtano preko CreateGraphics | crtati u Paint; podatke menjati drugde i pozvati Invalidate |
| Ništa se ne crta iz Form1\_Load | forma još nije prikazana kada se Load izvršava | crtati u Paint ili na klik dugmeta |
| Metoda Paint se uopšte ne poziva | metoda je prekucana, a nije povezana sa događajem | napraviti je dvoklikom na Paint u Properties → Events |
| Greška kod `new PointF(10.5, 20.5)` | 10.5 je tipa double | sufiks f: `10.5f` |
| Krug nije tamo gde treba | x, y su gornji levi ugao opisanog pravougaonika, ne centar | `cx - r, cy - r, 2 * r, 2 * r` |
| Animacija treperi | forma se briše i crta direktno na ekranu | DoubleBuffered = true |
| Isti „slučajni“ brojevi | `new Random()` u svakom kliku | jedan objekat Random kao polje klase |
| Luk je nacrtan na pogrešnoj strani | uglovi idu u smeru kazaljke na satu, 0° je desno | 90° je dole, 270° gore |

### Za ponavljanje i rad kod kuće

Portal Petlja.org (preporuka: uradite primere kućice i čiča-gliše):

- [Grafika u okruženju Visual Studio](https://petlja.org/biblioteka/r/lekcije/gui_cs-sr-cyrl/2_crtanje-crtanje_01_osnove#visual-studio)
- [Crtanje pravougaonika, elipsi i krugova](https://petlja.org/biblioteka/r/lekcije/gui_cs-sr-cyrl/2_crtanje-crtanje_02_oblici1#id1)
- [Crtanje linije](https://petlja.org/biblioteka/r/lekcije/gui_cs-sr-cyrl/2_crtanje-crtanje_03_oblici2#id2)
- [Video – vežbe iz crtanja grafike (YouTube)](https://www.youtube.com/watch?v=5qxAyKmce1Y)
