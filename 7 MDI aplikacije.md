# MDI aplikacije

Programiranje, IV razred · Visual C# 2010 Express

## Šta je MDI

MDI aplikacija ima jedan glavni prozor u kome se otvara više manjih prozora; oni ne mogu da izađu van njega.

Sve aplikacije koje ste do sada pravili bile su **SDI** (Single Document Interface – jedan dokument po prozoru): svaki prozor je samostalan na ekranu. **MDI** (Multiple Document Interface – više dokumenata u jednom prozoru) radi drugačije: glavni prozor je kao radni sto, a ostali prozori su papiri na tom stolu. Takav je i Visual C# 2010 Express: više datoteka koda otvoreno je unutar jednog prozora.

|  | SDI | MDI |
| --- | --- | --- |
| Prozori | samostalni, bilo gde na ekranu | unutar glavnog prozora |
| Meni | svaki prozor može imati svoj | jedan meni, na glavnom prozoru |
| Raspored prozora | korisnik ih sam pomera | aplikacija ih ređa jednim klikom |
| Primer | Paint, kalkulator | Visual C# 2010 Express |

### Dva nova pojma

| Pojam | Šta je | Kako se podešava |
| --- | --- | --- |
| roditeljska forma | glavni prozor, „radni sto“ | u Properties: **IsMdiContainer = True** |
| dete-forma | prozor koji se otvara unutar roditelja | u kodu: **prozor.MdiParent = this;** |

## Primer korak po korak: Moji prozori

Pravimo aplikaciju u kojoj meni Prozor otvara nove prozore unutar glavnog prozora i raspoređuje ih. U svaki prozor može da se upiše kratka beleška.

### Korak 1 – Glavna (roditeljska) forma

Napravite novi projekat Windows Forms Application pod imenom **MojiProzori**.

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | Form1 | Text: Moji prozori, Size: 800; 550, StartPosition: CenterScreen, **IsMdiContainer: True** |

Kada postavite IsMdiContainer na True, unutrašnjost forme postane tamnosiva. To je „radni sto“ na kome će se otvarati prozori. Na Form1 ne stavljajte ni dugmad ni polja za tekst: prekrili bi prozore.

### Korak 2 – Meni

Prevucite MenuStrip na Form1 i napravite jedan meni:

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| ToolStripMenuItem | mnuProzor | Text: `&Prozor` |
|  u Prozor | mnuNovi | Text: `&Novi prozor`, ShortcutKeys: Ctrl+N |
|  u Prozor | (separator) | – |
|  u Prozor | mnuKaskadno | Text: Kaskadno |
|  u Prozor | mnuHorizontalno | Text: Horizontalno |
|  u Prozor | mnuVertikalno | Text: Vertikalno |
|  u Prozor | (separator) | – |
|  u Prozor | mnuZatvoriSve | Text: Zatvori sve |
|  u Prozor | mnuIzlaz | Text: `&Izlaz` |

### Korak 3 – Dete-forma

U Solution Explorer-u: desni klik na projekat → **Add → Windows Form**, ime **frmDete.cs**.

| Kontrola | Name | Svojstva u Properties |
| --- | --- | --- |
| Form | frmDete | Size: 300; 200 |
| TextBox | txtBeleska | Multiline: True, Dock: Fill |

U frmDete ne pišemo nikakav kod.

### Korak 4 – Kod u Form1

Otvorite kod Form1 (F7). U klasu Form1, iznad konstruktora, dodajte polje koje broji otvorene prozore:

```csharp
private int broj = 0;   // za naslove "Prozor 1", "Prozor 2"...
```

Dvoklikom na stavke menija napravite metode za obradu događaja.

**Novi prozor** – najvažniji deo lekcije:

```csharp
private void mnuNovi_Click(object sender, EventArgs e)
{
    broj++;
    frmDete prozor = new frmDete();   // 1. napravimo prozor
    prozor.Text = "Prozor " + broj;   // 2. damo mu naslov
    prozor.MdiParent = this;          // 3. kažemo mu da je unutar Form1
    prozor.Show();                    // 4. prikažemo ga
}
```

**Raspored prozora** – svaka stavka ima jedan red:

```csharp
private void mnuKaskadno_Click(object sender, EventArgs e)
{
    this.LayoutMdi(MdiLayout.Cascade);          // jedan preko drugog, stepenasto
}

private void mnuHorizontalno_Click(object sender, EventArgs e)
{
    this.LayoutMdi(MdiLayout.TileHorizontal);   // jedan ispod drugog
}

private void mnuVertikalno_Click(object sender, EventArgs e)
{
    this.LayoutMdi(MdiLayout.TileVertical);     // jedan pored drugog
}
```

**Zatvori sve i Izlaz:**

```csharp
private void mnuZatvoriSve_Click(object sender, EventArgs e)
{
    foreach (Form dete in this.MdiChildren)   // svi prozori unutar Form1
    {
        dete.Close();
    }
}

private void mnuIzlaz_Click(object sender, EventArgs e)
{
    this.Close();   // zatvara Form1, a sa njom i sve prozore
}
```

### Objašnjenje

| Deo koda | Značenje |
| --- | --- |
| prozor.MdiParent = this; | ovaj red pravi MDI: prozor se otvara unutar Form1 (this) |
| this.LayoutMdi(...) | Form1 sama ređa sve svoje prozore |
| this.MdiChildren | niz svih prozora otvorenih unutar Form1 |
| foreach (Form dete in ...) | prolazi kroz svaki prozor i zatvara ga |

### Korak 5 – Lista prozora u meniju (bez koda)

Kliknite na traku menija (menuStrip1), na prazan deo pored stavke Prozor. U Properties pronađite **MdiWindowListItem** i izaberite **mnuProzor**. Na dnu menija Prozor sada će se sama pojavljivati lista otvorenih prozora, sa kvačicom pored aktivnog.

## Isprobajte

1. Pokrenite program (F5) i tri puta pritisnite **Ctrl+N**: otvaraju se Prozor 1, Prozor 2 i Prozor 3 unutar glavnog prozora.
2. Pokušajte da izvučete jedan prozor van glavnog: ne može, samo se sakrije iza ivice.
3. U svaki prozor upišite po jednu reč.
4. Probajte redom Kaskadno, Horizontalno i Vertikalno.
5. Otvorite meni Prozor: na dnu je lista prozora. Izaberite Prozor 1: on dolazi napred.
6. **Zatvori sve**: glavni prozor je prazan. Ponovo Ctrl+N: novi prozor se zove Prozor 4, jer brojanje nastavlja dalje.

## Česte greške

| Šta vidite | Uzrok | Rešenje |
| --- | --- | --- |
| Greška pri redu prozor.MdiParent = this; | na Form1 IsMdiContainer nije True | u Properties forme Form1 postavite IsMdiContainer: True |
| Prozor se otvara van glavnog prozora | nedostaje red prozor.MdiParent = this; | dodajte ga pre prozor.Show(); |
| Prozori se otvaraju iza dugmeta ili polja na Form1 | na roditeljsku formu su stavljene kontrole | na Form1 ostavite samo meni |
| U meniju nema liste prozora | MdiWindowListItem je traženo na stavci Prozor, a ne na traci menija | izaberite menuStrip1 i tu postavite MdiWindowListItem |
| frmDete je podvučeno crveno | ime forme u kodu nije isto kao u Solution Explorer-u | prepišite ime tačno, sa istim velikim i malim slovima |

## Rezime

- MDI aplikacija ima roditeljsku formu u kojoj se otvaraju dete-forme.
- Roditelj: u Properties **IsMdiContainer = True**.
- Dete: `new`, zatim **MdiParent = this**, pa `Show()`.
- `this.LayoutMdi(...)` ređa prozore, a `this.MdiChildren` su svi otvoreni prozori.
- Lista prozora u meniju: svojstvo **MdiWindowListItem** trake menija, bez koda.

## Pitanja za proveru

1. Po čemu se MDI aplikacija razlikuje od SDI aplikacije? Navedite po jedan primer.
2. Koje svojstvo glavne forme mora biti True i šta se desi ako nije?
3. Šta radi red `prozor.MdiParent = this;` i šta bi se desilo bez njega?
4. Koja je razlika između rasporeda Horizontalno i Vertikalno?

## Zadatak za vežbu

1. Dodajte u meni stavku **Novi crveni prozor**, koja otvara dete-formu sa crvenom pozadinom (`prozor.BackColor = Color.Red;`) i naslovom „Crveni prozor“.
2. Promenite program tako da se posle svakog novog prozora prozori sami poslože kaskadno.

*Za višu ocenu:* dodajte paletu komandi (ToolStrip) sa dugmadima Novi, Kaskadno i Zatvori sve, koja koriste iste metode za obradu događaja kao stavke menija, bez novog koda.
