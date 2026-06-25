# PP8

## Goal

In this exercise you will:

* Parse **command-line flags** and **parameters** in C using `getopt` and `atoi`.
* Prompt for **interactive** input using `scanf` and demonstrate file scanning with `fscanf`.
* Read input from **standard input** via shell redirection.
* Implement and modularize a **symmetric Caesar cipher** and extend it to a **prototype asymmetric** XOR cipher, compiling and linking multiple source files.

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. When time is up, stop immediately and record exactly where you paused.

---

## Workflow

1. **Fork** this repository on GitHub.
2. **Clone** your fork locally.
3. Create a `solutions/` directory at the project root:

   ```bash
   mkdir solutions
   ```
4. Add each task’s source file under `solutions/` (e.g., `solutions/getopt_flags.c`).
5. **Commit** and **push** your changes to GitHub.
6. **Submit** your GitHub repository link for review.

---

## Prerequisites

* GNU C compiler (`gcc`).
* Familiarity with:

  * `getopt` (include `<unistd.h>`)
  * `atoi` (include `<stdlib.h>`)
  * Standard I/O: `printf`, `scanf`, `fscanf`, `fgets`
  * Shell I/O redirection
  * Manual compilation and linking
* Consult man-pages:

  ```bash
  man getopt   # option parsing
  man atoi     # string to integer conversion
  man scanf    # interactive input
  man fscanf   # file scanning
  ```

---

## Tasks

### Task 1: Command-line Flags Only

**Objective:** Parse simple flags without arguments.

1. Create `solutions/getopt_flags.c`:

   ```c
   #include <stdio.h>
   #include <stdlib.h>
   #include <unistd.h>

   int main(int argc, char *argv[]) {
       int opt;
       int flag_a = 0, flag_b = 0;

       while ((opt = getopt(argc, argv, "ab")) != -1) {
           if      (opt == 'a') flag_a = 1;
           else if (opt == 'b') flag_b = 1;
           else {
               fprintf(stderr, "Usage: %s [-a] [-b]\n", argv[0]);
               exit(EXIT_FAILURE);
           }
       }

       printf("flag_a=%d, flag_b=%d\n", flag_a, flag_b);
       return 0;
   }
   ```
2. Compile and test:

   ```bash
   gcc -o solutions/getopt_flags solutions/getopt_flags.c
   ./solutions/getopt_flags -a -b
   ```

---

### Task 2: Command-line Parameters & File Names

**Objective:** Parse options with arguments, including input and output file names.

1. Create `solutions/getopt_params.c`:

   ```c
   #include <stdio.h>
   #include <stdlib.h>
   #include <unistd.h>

   int main(int argc, char *argv[]) {
       int opt;
       int number = 0;
       char *str = NULL;
       char *infile = NULL, *outfile = NULL;

       while ((opt = getopt(argc, argv, "n:s:i:o:")) != -1) {
           if      (opt == 'n') number  = atoi(optarg);
           else if (opt == 's') str     = optarg;
           else if (opt == 'i') infile  = optarg;
           else if (opt == 'o') outfile = optarg;
           else {
               fprintf(stderr, "Usage: %s -n num -s str -i infile -o outfile\n", argv[0]);
               exit(EXIT_FAILURE);
           }
       }

       printf("number=%d, string=%s, infile=%s, outfile=%s\n",
              number,
              str     ? str     : "(null)",
              infile  ? infile  : "(none)",
              outfile ? outfile : "(none)");
       return 0;
   }
   ```
2. Compile and test:

   ```bash
   gcc -o solutions/getopt_params solutions/getopt_params.c
   ./solutions/getopt_params -n 42 -s hello -i input.txt -o output.txt
   ```

#### Reflection Questions

1. **How do you pass a file name to a program using the `-i` and `-o` options?**
Mit -i wird die Eingabedatei und mit -o die Ausgabedatei über die Kommandozeile angegeben, z. B. -i input.txt -o output.txt.
   
2. **What are typical use cases for parameters versus flags? How do the differ from one another?**
Flags schalten Funktionen ein oder aus (z. B. -a), Parameter übergeben zusätzliche Werte wie Zahlen, Texte oder Dateinamen (z. B. -n 42).

---

### Task 3: Interactive Input with `scanf` & `fscanf`

**Objective:** Prompt for user input at runtime and read structured data from a file.

1. Create `solutions/interactive.c`:

   ```c
   #include <stdio.h>
   #define BUF_SIZE 100

   int main(void) {
       char name[BUF_SIZE];
       int age;

       printf("Enter your name: ");
       scanf("%99s", name);
       printf("Hello, %s!\n", name);

       printf("Enter your age: ");
       scanf("%d", &age);
       printf("You are %d years old.\n", age);

       // File scanning
       FILE *fp = fopen("data.txt", "r");
       if (!fp) { perror("fopen"); return 1; }
       char fname[BUF_SIZE];
       int fage;
       if (fscanf(fp, "%99s %d", fname, &fage) == 2) {
           printf("File data: %s is %d years old.\n", fname, fage);
       }
       fclose(fp);
       return 0;
   }
   ```
2. Provide `solutions/data.txt` containing:

   ```text
   Alice 30
   ```
3. Compile and run:

   ```bash
   gcc -o solutions/interactive solutions/interactive.c
   ./solutions/interactive
   ```

#### Reflection Question

* **Why is a run-to-completion (batch) approach often preferable to interactive input?**
Batch-Verarbeitung ermöglicht automatische und wiederholbare Abläufe ohne Benutzereingriffe.
Sie eignet sich besser für Skripte und die Verarbeitung großer Datenmengen.
---

### Task 4: Input Redirection from STDIN

**Objective:** Read input supplied via shell redirection.

1. Create `solutions/redirect_input.c`:

   ```c
   #include <stdio.h>
   #define BUF_SIZE 256

   int main(void) {
       char buf[BUF_SIZE];
       wenn (fgets(buf, BUF_SIZE, stdin))
           printf("Sie haben eingegeben: %s", buf);
       returniere 0;
   }
   ```
2. Erstellen `Lösungen/Eingabe.txt` mit Beispieltext.
3. Kompilieren und ausführen:

   ```bash
   gcc -o Lösungen/redirect_input Lösungen/redirect_input.c
   ./lösungen/redirect_input < Lösungen/input.txt
   ```

#### Reflexionsfrage

* **Was ist der Unterschied zwischen der Weiterleitung zu stdin und dem expliziten Öffnen einer Datei mit `fopen`?**
Bei stdin-Umgebung liest das Programm Daten aus der Standard-Eingabe, ohne den Terminamen zu kennen.
fopen öffnet eine beste Datumsangabe im Programmcode.

---

### Aufgabe 5: Caesar-Chiffre und Prototyp einer asymmetrischen XOR-Chiffre

**Ziel:** Implementieren und modularisieren Sie zwei Chiffrieralgorithmen.

#### 5.1 Symmetrische Caesar-Chiffre

1. **Kopfzeile (`Chiffre.h`)**:

   ```c
   #ifndef CHIFFRE_H
   #define CHIFFRE_H
   char encrypt_char(char c, int shift);
   char decrypt_char(char c, int shift);
   #endif
   ```
2. **Umsetzung (`Chiffre.c`)**:

   ```c
   #include "chiffre.h"

   char encrypt_char(char c, int shift) {
       wenn (c >= 'A' && c <= 'Z') returniere 'A' + (c - 'A' + shift) % 26;
       wenn (c >= 'a' && c <= 'z') returniere 'a' + (c - 'a' + shift) % 26;
       c zurückgeben;
   }

   char decrypt_char(char c, int shift) {
       returniere encrypt_char(c, 26 - (Umschalt % 26));
   }
   ```
3. **Treiber (`Caesar.c`)**:

   ```c
   #include <stdio.h>
   #include <stdlib.h>
   #include <unistd.h>
   #include "chiffre.h"

   void usage(const char *prog) {
       fprintf(stderr, "Verwendung: %s -e shift|-d shift -i infile -o outfile\n", prog);
       exit(EXIT_FAILURE);
   }

   int main(int argc, char *argv[]) {
       int opt, shift = 0, enc = -1;
       char *infile = NULL, *outfile = NULL;

       würrend ((opt = getopt(argc, argv, "e:d:i:o:")) != -1) {
           if (opt == 'e') { shift = atoi(optarg); enc = 1; }
           sonst wenn (opt == 'd') { shift = atoi(optarg); enc = 0; }
           sonst wenn (opt == 'i') infile = optarg;
           sonst wenn (opt == 'o') Ausgabedatei = optarg;
           sonst Bearbeitung (argv [0]);
       }

       wenn (enc < 0 || !Einfügen || !Ausgabedatei) Bearbeitung (argv [0]);

       DATI *fin = fopen(infile, "r");
       wenn (!fin) { perror("fopen infile"); exit(EXIT_FAILURE); }
       DATI *fout = fopen(outfile, "w");
       wenn (!fout) { perror("fopen outfile"); exit(EXIT_FAILURE); }

       int c;
       while ((c = fgetc (fin))!= EOF) {
           Auschargen;
           wenn (enc)
 Aufgabe 5: Caesar-Chiffre und Prototyp einer asymmetrischen XOR-Chiffre
---
fopen öffnet eine beste Datumsangabe im Programmcode.
           fputc(out, fout);
       }

       fclose(fin);
       fclose(fout);
       Rückkehrer 0;
   }
   ```
4. **Kompilieren und Verlinken**:

   ```bash
   gcc -c solutions/cipher.c -o solutions/cipher.o
   gcc -c solutions/caesar.c -o solutions/caesar.o
   gcc solutions/caesar.o solutions/cipher.o -o solutions/caesar
   ```
5. **Test**:

   ```bash
   ./solutions/caesar -e 3 -i input.txt -o enc.txt
   ./solutions/caesar -d 3 -i enc.txt -o dec.txt
   ```

#### 5.2 Prototyp eines asymmetrischen XOR-Chiffre

1. **Kopfzeile (`asym.h`)**:

   ```c
   #ifndef ASYM_H
   #define ASYM_H
   char encrypt_xor(char c, char key);
   char decrypt_xor (char c, char key);
   #endif
   ```
2. **Umsetzung (`asym.c`)**:

   ```c
   #include "asym.h"

   char encrypt_xor(char c, char key) { return c ^ key; }
   char decrypt_xor(char c, char key) { return c ^ key; }
   ```
3. **Treiber (`advanced_cipher.c`)**:

   ```c
   #include <stdio.h>
   #include <stdlib.h>
   #include <string.h>
   #include <unistd.h>
   #include "chiffre.h"
   #include "asym.h"

   void usage(const char *prog) {
       fprintf(stderr, "Verwendung: %s -m <caesar|xor> -e key|-d key -i infile -o outfile\n", prog);
       exit(EXIT_FAILURE);
    DATI *fin = fopen(infile, "r"); DATI *fin = fopen(infile, "r");}

   int main(int argc, char *argv[]) {
       int opt, enc = -1, Schlüssel = 0;
    wenn (!fout) { perror("fopen outfile"); exit(EXIT_FAILURE); } char *mode = NULL, *infile = NULL, *outfile = NULL;

       würrend ((opt = getopt(argc, argv, "m:e:d:i:o:")) != -1) {
           wenn (opt == 'm') Modus = optarg;
           sonst wenn (opt == 'e') { Schlüssel = atoi(optarg); enc = 1; }
           sonst wenn (opt == 'd') { Schlüssel = atoi(optarg); enc = 0; }
           sonst wenn (opt == 'i') infile = optarg;
           sonst wenn (opt == 'o') Ausgabedatei = optarg;
           sonst Bearbeitung (argv [0]);
       }

       wenn (!Modus || enc < 0 || !Einfügen || !Ausgabedatei) Bearbeitung (argv [0]);

       DATI *fin = fopen(infile, "r");
       wenn (!fin) { perror("fopen infile"); exit(EXIT_FAILURE); }
       DATI *fout = fopen(outfile, "w");
       wenn (!fout) { perror("fopen outfile"); exit(EXIT_FAILURE); }

       int c;
       while ((c = fgetc (fin))!= EOF) {
           Auschargen;
4. **Kompilieren und Verlinken**: wenn (enc < 0 || !Einfügen || !Ausgabedatei) Bearbeitung (argv [0]);
               wenn (enc)
                   out = encrypt_char(c, Schlüssel);
               sonst
   **
Test } sonst wenn (strcmp (Modus, "xor") == 0) {
               wenn (enc)
                   out = encrypt_xor(c, (char)key);
               sonst
                   out = decrypt_xor(c, (char)key);
           } sonst {
               Bearbeitung (argv [0]);
           }
           fputc(out, fout);
       }

       fclose(fin);
       fclose(fout);
       Rückkehrer 0;
   }
   ```
4. **Kompilieren und Verlinken**:

   ```bash
   gcc -c solutions/cipher.c solutions/asym.c solutions/advanced_cipher.c
   gcc solutions/cipher.o solutions/asym.o solutions/advanced_cipher.o -o solutions/advanced_cipher
   ```
5. **Testen Sie beide Modi**:

   ```bash
   ./solutions/advanced_cipher -m caesar -e 3 -i input.txt -o enc.txt
   ./solutions/advanced_cipher -m caesar -d 3 -i enc.txt -o dec.txt
   ./solutions/advanced_cipher -m xor -e 42 -i input.txt -o xor_enc.txt
   ./solutions/advanced_cipher -m xor -d 42 -i xor_enc.txt -o xor_dec.txt
   ```

#### Reflexionsfrage

* **Erklären Sie in Ihren eigenen Worten, war die Versicherungs- und Entschlösungsforschung in beiden Chiffren bewirken.**
Die Caesar-Chiffre verschiebt Buchstaben um eine feste Anzahl von Stellen; Entschlosselung verschiebt sie zurück.
Die XOR-Chiffre verknüpft jedes Zeichen mit einem Schlüssel per XOR, wobei derselbe Schlüssel zum Ent- und Verschlüsseln genutzt wird.
---

**Denken Sie an Daran:** Nach dem Ende anhalten **90 Minuten** und notieren Sie, wo Sie angehalten haben.
