# NSA Whistleblower

We're provided with a [PDF file](https://github.com/dogelition/ctf-writeups/blob/master/2020/ALLES/NSA%20Whistleblower/NSA_leak.pdf) and the description "Psst... The most secret data is hidden in plain sight".

Unfortunately, we weren't able to solve this challenge during the CTF. We tried various PDF tools, inspected the images, looked for hidden stuff in the fonts, the text, etc. A few days after the CTF ended, we finally found the solution and realized how simple it actually was.

All you have to do is zoom in on the PDF or mess with the colors to realize that there are yellow dots all over the pages. A quick search for "yellow dots" leads to a [wikipedia article](https://en.wikipedia.org/wiki/Machine_Identification_Code), explaining that these dots are produced by certain printers (the provided PDF definitely hasn't been through a printer, but whatever...). We then found [deda](https://github.com/dfd-tud/deda) which can read the data encoded using these dots.

We can use `pdftoppm` to convert the PDF pages to .png images so that deda can read them:

```
pdftoppm NSA_leak.pdf nsa -png
```

This produces files named `nsa-1.png` through `nsa-8.png`.

We can now invoke deda on the images:

```
deda_parse_print nsa-1.png
```

Output:

```
Detected pattern 4


_|0|1|2|3|4|5|6|7
0|
1|.
2|.
3|. .           .
4|  .     . .
5|  .     . .
6|.
7|.
8|    .   .   .
9|            .
0|          .
1|          .
2|          .
3|.
4|.     .   .
5|.         . .
        27 dots.



<TDM of Pattern 4 at 0.00 x -0.00 inches>
Decoded:
        manufacturer: Xerox
        serial: -657676-
        timestamp: 2042-02-04 04:20:00
        raw: 0000657676000042020404040020
        minutes: 20
        hour: 04
        day: 04
        month: 02
        year: 42
        unknown1: 00
        unknown3: 00
        unknown4: 00
        unknown5: 00
        printer: 00657676
```

The timestamp basically just consists of the number `42`, so it's not very interesting. But the serial seems to be the first 3 characters of the flag (`ALL`), encoded in decimal! If we concatenate the serial numbers from all pages and put spaces in the right spots, we end up with this decimal sequence:

`65 76 76 69 83 123 115 51 99 114 51 116 95 100 48 116 115 125`

After decoding it using "From Decimal" on [CyberChef](https://gchq.github.io/CyberChef), we get the flag:

`ALLES{s3cr3t_d0ts}`
