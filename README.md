# Praktikum Sistem Kendali - Arduino UNO
Pinout Arduino Uno

![Alt text](https://github.com/yogidm/Sistem-Kendali/blob/main/Arduino-UNO-Parts-Marking.jpg "Optional Title")

## Jobsheet 1: Instalasi Arduino

### Tujuan
- Mahasiswa mampu melakukan instalasi software Arduino IDE  
- Mahasiswa mampu melakukan konfigurasi port dan board Arduino Mega 2560  
- Mahasiswa mampu melakukan upload program sederhana  

### Alat dan Bahan
- Arduino Mega 2560  
- Kabel USB Arduino  
- Laptop dengan Arduino IDE terbaru  

### Langkah Kerja
1. Unduh dan instal **Arduino IDE** dari situs resmi.  [arduino.cc](arduino.cc)
3. Hubungkan Arduino Mega 2560 ke laptop menggunakan kabel USB.  
4. Buka Arduino IDE → Pilih **Tools → Board → Arduino Mega 2560**.  
5. Pilih **Tools → Port → COM...** sesuai perangkat.  
6. Uji coba dengan program **Blink** bawaan IDE:  
   - Klik **File → Examples → 01.Basics → Blink**.  
   - Upload ke board.  
7. Amati LED pada pin 13 yang akan berkedip.

## Instalasi Driver Arduino Mega 2560

### Windows
1. Hubungkan board Arduino Mega 2560 ke laptop/PC dengan kabel USB.  
2. Windows biasanya akan mendeteksi perangkat otomatis. Jika driver belum terpasang:  
   - Buka **Device Manager** (`Win + X` → *Device Manager*).  
   - Cari bagian **Ports (COM & LPT)** → akan muncul *Arduino Mega 2560 (COMx)*.  
   - Jika ada tanda kuning, berarti driver belum terpasang.  
3. Buka **Arduino IDE** → menu **Tools → Board** pilih *Arduino Mega 2560*.  
4. Jika driver masih belum dikenali:  
   - Unduh driver **CH340/ATmega16U2** (tergantung versi board) dari situs resmi atau GitHub.  
   - Instal dengan klik kanan → *Run as Administrator*.  
   - Setelah selesai, cabut dan pasang kembali board.  
5. Panduan tambahan instalasi driver CH340G dapat dilihat di:  
   [Cara Install Driver Arduino CH340G](http://yogi.blog.um.ac.id/2020/11/01/cara-install-driver-arduino-ch340g/)  

---

### Tugas
- Buat modifikasi program Blink dengan interval 200 ms dan 1 detik.  

---

## Jobsheet 2: Potensiometer untuk Mengatur Kecerahan LED

### Tujuan
- Mahasiswa memahami penggunaan input analog (potensiometer).  
- Mahasiswa mampu mengendalikan PWM LED dengan potensiometer.  

### Alat dan Bahan
- Arduino Mega 2560  
- Breadboard  
- LED + resistor 220Ω  
- Potensiometer 10kΩ  
- Kabel jumper  

### Rangkaian
- Potensiometer:  
  - Pin tengah → A0 Arduino Mega  
  - Sisi kiri → 5V  
  - Sisi kanan → GND  
- LED:  
  - Anoda → pin 9 Arduino Mega (PWM)  
  - Katoda → resistor → GND  

### Kode Program
```cpp
int potPin = A0;
int ledPin = 9;
int potValue = 0;
int brightness = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  potValue = analogRead(potPin);        
  brightness = map(potValue, 0, 1023, 0, 255); 
  analogWrite(ledPin, brightness);      
}
```

### Tugas
- Ubah program sehingga potensiometer mengatur kecepatan **kedip LED** bukan kecerahannya.  

---

## Jobsheet 3: Menggunakan LDR untuk Mengatur Kecerahan LED

### Tujuan
- Mahasiswa memahami sensor cahaya (LDR).  
- Mahasiswa mampu membuat sistem otomatisasi berdasarkan intensitas cahaya.  

### Alat dan Bahan
- Arduino Mega 2560  
- Breadboard  
- LED + resistor 220Ω
- LDR (Light Dependent Resistor)  
- Resistor 10kΩ (sebagai pull-down)  
- Kabel jumper  

### Rangkaian
- LDR dan resistor 10kΩ dibuat **pembagi tegangan**:  
  - LDR -> 5V  
  - Resistor 10kΩ -> GND  
  - Titik tengah -> A0 Arduino Mega  
- LED:  
  - Anoda -> pin 9 Arduino Mega (PWM)  
  - Katoda -> resistor -> GND  

### Kode Program
```cpp
int ldrPin = A0;
int ledPin = 9;
int ldrValue = 0;
int brightness = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  ldrValue = analogRead(ldrPin);          
  brightness = map(ldrValue, 0, 1023, 255, 0); 
  analogWrite(ledPin, brightness);        
  Serial.println(ldrValue);               
  delay(100);
}
```

### Tugas
- Modifikasi program agar LED hanya menyala jika ruangan gelap (misalnya nilai LDR < 500).

---


# JOBSHEET 4  
## SISTEM KONTROL SUHU MENGGUNAKAN SENSOR LM35 DAN LED

### A. Tujuan Pembelajaran  
1. Mahasiswa mampu memahami prinsip kerja sensor suhu LM35.  
2. Mahasiswa mampu membaca data suhu menggunakan Arduino.  
3. Mahasiswa mampu mengontrol LED berdasarkan suhu (open loop dan close loop).  
4. Mahasiswa mampu merekam data suhu ke file CSV menggunakan CoolTerm dan menganalisisnya di Excel.

---

### B. Alat dan Bahan  
- 1 unit Arduino Uno  
- 1 buah sensor suhu LM35  
- 3 buah LED (Merah, Kuning, Hijau)  
- 3 buah resistor 220 Ohm
- 1 buah Breadboard  
-  kabel jumper secukupnya
- 1 kabel USB  
- Aplikasi **CoolTerm** untuk logging data

---

### C. Dasar Teori  
Sensor **LM35** adalah sensor suhu analog yang menghasilkan tegangan keluaran sebanding dengan suhu dalam skala Celsius.  
Setiap perubahan **10 mV** mewakili **1°C**.  
Arduino membaca nilai analog (0–1023) dari LM35 pada rentang tegangan 0–5V, sehingga suhu dapat dihitung dengan rumus:

`Suhu (°C) = (Nilai_ADC × 5.0 × 100) / 1023`


Rumus ini berasal dari konversi nilai ADC ke tegangan (`ADC × 5 / 1023`), lalu dikonversi ke suhu dengan faktor 100 karena 1°C = 10 mV = 0.01 V.  
Dengan demikian, setiap kenaikan tegangan 0.01 volt pada output LM35 menunjukkan kenaikan suhu sebesar 1°C.

---

## KEGIATAN 1 - Sistem Open Loop (Pembacaan Sensor Saja)

### Tujuan  
Membaca suhu dari sensor LM35 tanpa memengaruhi output (monitoring saja).

### Langkah Percobaan  
1. Hubungkan pin LM35 sebagai berikut:  
   - VCC → 5V Arduino  
   - OUT → A0 Arduino  
   - GND → GND Arduino  
2. Upload program berikut ke Arduino.

### Kode Program
```cpp
int sensorPin = A0;
float suhu;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int adcValue = analogRead(sensorPin);
  suhu = (adcValue * 5.0 * 100.0) / 1024.0;
  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.println(" °C");
  delay(1000);
}
```

### Analisis  
Amati hasil pembacaan suhu pada **Serial Monitor**. Perhatikan bahwa output tidak berubah meskipun suhu naik atau turun.

---

## KEGIATAN 2 - Sistem Close Loop (Kontrol 3 LED Berdasarkan Suhu)

### Tujuan  
Menyalakan LED sesuai dengan batas suhu tertentu.

### Langkah Percobaan  
1. Hubungkan tiga LED dengan resistor ke pin berikut:  
   - LED Hijau → pin 8  
   - LED Kuning → pin 9  
   - LED Merah → pin 10  
2. Upload program berikut.

### Kode Program
```cpp
int sensorPin = A0;
int ledHijau = 8;
int ledKuning = 9;
int ledMerah = 10;
float suhu;

void setup() {
  Serial.begin(9600);
  pinMode(ledHijau, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledMerah, OUTPUT);
}

void loop() {
  int adcValue = analogRead(sensorPin);
  suhu = (adcValue * 5.0 * 100.0) / 1024.0;

  if (suhu < 30) {
    digitalWrite(ledHijau, HIGH);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, LOW);
  } else if (suhu < 35) {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, HIGH);
    digitalWrite(ledMerah, LOW);
  } else {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, HIGH);
  }

  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.println(" °C");
  delay(1000);
}
```

### Analisis  
Amati perubahan LED saat suhu sensor meningkat atau menurun.

---

## KEGIATAN 3 - Kontrol Proporsional Warna LED

### Tujuan  
Membuat perubahan warna LED sebanding (proporsional) terhadap kenaikan suhu.

### Langkah Percobaan  
1. Gunakan kembali tiga LED dengan pin yang sama.  
2. Upload program berikut.

### Kode Program
```cpp
int sensorPin = A0;
int ledHijau = 8;
int ledKuning = 9;
int ledMerah = 10;
float suhu;

void setup() {
  Serial.begin(9600);
  pinMode(ledHijau, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledMerah, OUTPUT);
}

void loop() {
  int adcValue = analogRead(sensorPin);
  suhu = (adcValue * 5.0 * 100.0) / 1024.0;

  // Mapping proporsional antara 20°C - 40°C
  int intensitasHijau = map(suhu, 20, 40, 255, 0);
  int intensitasMerah = map(suhu, 20, 40, 0, 255);
  int intensitasKuning = 128;

  analogWrite(ledHijau, constrain(intensitasHijau, 0, 255));
  analogWrite(ledKuning, intensitasKuning);
  analogWrite(ledMerah, constrain(intensitasMerah, 0, 255));

  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.println(" °C");
  delay(500);
}
```

### Analisis  
Amati transisi warna LED yang berubah perlahan seiring perubahan suhu.

---

## KEGIATAN 4 - Logging Data Suhu ke File CSV

### Tujuan  
Merekam data suhu Arduino ke komputer dan memplot grafik di Microsoft Excel.

### Langkah Percobaan  
1. Unduh dan pasang aplikasi **CoolTerm** dari situs resmi:  
   [https://freeware.the-meiers.org](https://freeware.the-meiers.org)  
2. Jalankan CoolTerm, pilih **Connection → Options → Serial Port**, atur:  
   - Port: COM Arduino Anda  
   - Baudrate: 9600  
   - Data Bits: 8, Stop Bits: 1, Parity: None  
3. Klik **Connect**. Data suhu akan tampil secara real-time.  
4. Untuk menyimpan data, pilih **Connection → Capture to Textfile → Start**.  
   Simpan dengan nama `data_suhu.csv`.  
5. Setelah selesai, pilih **Stop Capture**.  
6. Buka file `.csv` tersebut di **Microsoft Excel**, lalu buat grafik garis suhu terhadap waktu.

### Kode Program
Gunakan kode dari **Kegiatan 1** (pembacaan suhu), karena sudah menampilkan data ke Serial.

```cpp
int sensorPin = A0;
float suhu;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int adcValue = analogRead(sensorPin);
  suhu = (adcValue * 5.0 * 100.0) / 1024.0;
  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.println(" °C");
  delay(1000);
}
```

### Analisis  
Bandingkan grafik suhu hasil pengukuran dengan kondisi lingkungan sekitar (misalnya ketika didekatkan ke tangan atau sumber panas).

---

### D. Pertanyaan Umum  
1. Apa perbedaan sistem open loop dan close loop pada kontrol suhu ini?  
2. Bagaimana pengaruh suhu terhadap perubahan intensitas LED?  
3. Mengapa diperlukan resistor pada rangkaian LED?  
4. Bagaimana cara meningkatkan akurasi pembacaan suhu LM35?

---




# JOBSHEET 5
## Kontrol Sensor Ultrasonik HC-SR04 dengan Arduino Uno

### A. Tujuan Pembelajaran
1. Memahami prinsip kerja sensor ultrasonik HC-SR04.  
2. Melakukan kalibrasi akurasi pembacaan jarak menggunakan Serial Monitor.  
3. Mengimplementasikan sistem indikator jarak aman menggunakan LED.  
4. Memvisualisasikan hasil pembacaan jarak menggunakan Serial Plotter.

---

### B. Alat dan Bahan
- Arduino Uno  
- Sensor Ultrasonik HC-SR04  
- 3 LED (Hijau, Kuning, Merah)  
- 3 resistor 220 Ohm
- Breadboard dan kabel jumper  
- Laptop dengan Arduino IDE  

---

### C. Dasar Teori Singkat
Sensor ultrasonik HC-SR04 bekerja dengan mengukur waktu pantulan gelombang suara dari objek. Gelombang ultrasonik dipancarkan melalui pin **Trigger**, kemudian pantulannya diterima oleh pin **Echo**. Waktu tempuh digunakan untuk menghitung jarak dengan rumus:

```
Jarak (cm) = (Waktu (microS)  x 0.0343) / 2
```

---

### D. Diagram Koneksi

| Komponen | Pin Arduino |
|-----------|--------------|
| VCC | 5V |
| GND | GND |
| Trig | Pin 9 |
| Echo | Pin 10 |
| LED Hijau | Pin 2 |
| LED Kuning | Pin 3 |
| LED Merah | Pin 4 |

---

### E. Kegiatan 1: Kalibrasi Akurasi Jarak (Serial Monitor)

**Tujuan:**  
Mengetahui akurasi hasil pembacaan jarak dari sensor ultrasonik.

**Langkah-langkah:**  
1. Rangkai sensor ultrasonik sesuai tabel koneksi.  
2. Upload kode berikut ke Arduino.  
3. Buka Serial Monitor dan amati hasil pengukuran.  
4. Catat selisih antara hasil sensor dan penggaris.

```cpp
#define trigPin 9
#define echoPin 10
long duration;
float distance;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.0343) / 2;

  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.println(" cm");
  delay(500);
}
```

**Tugas:**  
- Ukur jarak 5, 10, 15, dan 20 cm.  
- Hitung *error* (%) antara pembacaan sensor dan penggaris.

---

### F. Kegiatan 2: Indikator Jarak Aman Menggunakan LED

**Tujuan:**  
Membuat sistem indikator visual berdasarkan kategori jarak.

**Kriteria Jarak:**
- 10-13 cm -> LED **Hijau** (Aman)  
- 5-9.9 cm -> LED **Kuning** (Terlalu Dekat)  
- >13.1 cm -> LED **Merah** (Terlalu Jauh)

**Kode Program:**

```cpp
#define trigPin 9
#define echoPin 10
#define ledHijau 2
#define ledKuning 3
#define ledMerah 4

long duration;
float distance;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledHijau, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledMerah, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.0343) / 2;

  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.println(" cm");

  if (distance >= 10 && distance <= 13) {
    digitalWrite(ledHijau, HIGH);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, LOW);
  } 
  else if (distance >= 5 && distance < 10) {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, HIGH);
    digitalWrite(ledMerah, LOW);
  } 
  else if (distance > 13.1) {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, HIGH);
  } 
  else {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, LOW);
  }

  delay(500);
}
```

---

### G. Kegiatan 3: Visualisasi Data Jarak Menggunakan Serial Plotter

**Tujuan:**  
Menampilkan perubahan jarak secara dinamis dalam bentuk grafik menggunakan Serial Plotter.

**Langkah-langkah:**  
1. Gunakan rangkaian dari kegiatan sebelumnya.  
2. Upload kode berikut.  
3. Buka *Tools -> ’ Serial Plotter* di Arduino IDE.  
4. Gerakkan objek majuâ€“mundur di depan sensor dan amati perubahan grafik.

```cpp
#define trigPin 9
#define echoPin 10

long duration;
float distance;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.0343) / 2;

  Serial.println(distance); // hanya nilai jarak agar mudah diplot
  delay(100);
}
```

**Tugas:**
- Amati pola grafik ketika objek digerakkan perlahan dan cepat.  
- Gambarkan bentuk grafik pada lembar hasil pengamatan.  
- Jelaskan hubungan antara jarak dan waktu berdasarkan grafik tersebut.

---

### H. Pertanyaan
1. Apa penyebab perbedaan hasil antara jarak aktual dan hasil pembacaan sensor?  
2. Bagaimana cara meningkatkan akurasi sensor ultrasonik?  
3. Apa kelebihan Serial Plotter dibanding Serial Monitor dalam analisis data jarak?  
4. Bagaimana Anda mengembangkan sistem ini untuk aplikasi dunia nyata?

---


# JOBSHEET 6 - OLED Display dengan Animasi dan Variasi Tampilan

## Tujuan Pembelajaran
- Memahami prinsip kerja OLED 128x64 berbasis komunikasi I2C  
- Mampu menampilkan teks, grafik, dan animasi sederhana menggunakan library `Adafruit_SSD1306` dan `Adafruit_GFX`  
- Mengembangkan berbagai variasi animasi yang interaktif dan kreatif  

---

## Alat dan Bahan
- Arduino Uno  
- OLED Display 128x64 (I2C)  
- Kabel jumper  

### Koneksi Pin
| OLED | Arduino Uno |
|------|--------------|
| VCC  | 5V |
| GND  | GND |
| SDA  | A4 |
| SCL  | A5 |

---

## Kegiatan 1 - Menampilkan Teks Statis
Menampilkan teks sederhana sebagai pengenalan dasar tampilan OLED.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(10, 20);
  display.print("OLED Test");
  display.display();
}

void loop() {}
```

---

## Kegiatan 2 - Animasi Objek Bergerak (Bola Berjalan)
Menampilkan bola kecil yang bergerak dari kiri ke kanan.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
}

void loop() {
  for (int x = 0; x < SCREEN_WIDTH; x++) {
    display.clearDisplay();
    display.fillCircle(x, 32, 5, SSD1306_WHITE);
    display.display();
    delay(15);
  }
}
```

---

## Kegiatan 3 - Animasi Teks Bergerak (Marquee Text)
Membuat tulisan berjalan dari kanan ke kiri seperti papan pengumuman digital.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
}

void loop() {
  for (int x = 128; x > -80; x--) {
    display.clearDisplay();
    display.setTextSize(2);
    display.setTextColor(SSD1306_WHITE);
    display.setCursor(x, 25);
    display.print("Arduino!");
    display.display();
    delay(30);
  }
}
```

---

## Kegiatan 4 - Animasi Progress Bar
Membuat efek bar loading sebagai simulasi proses berjalan.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
}

void loop() {
  for (int i = 0; i <= 100; i++) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(SSD1306_WHITE);
    display.setCursor(30, 20);
    display.print("Loading...");
    display.drawRect(10, 35, 108, 10, SSD1306_WHITE);
    display.fillRect(10, 35, i, 10, SSD1306_WHITE);
    display.display();
    delay(50);
  }
}
```

---

## Kegiatan 5 - Animasi Rotasi Garis (Radar Effect)
Simulasi animasi radar dengan garis berputar.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <math.h>

#define PI 3.14159265358979323846
Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
}

void loop() {
  for (int angle = 0; angle < 360; angle += 5) {
    display.clearDisplay();
    int centerX = 64, centerY = 32, radius = 25;
    float rad = angle * PI / 180.0;
    int x = centerX + radius * cos(rad);
    int y = centerY + radius * sin(rad);
    display.drawCircle(centerX, centerY, radius, SSD1306_WHITE);
    display.drawLine(centerX, centerY, x, y, SSD1306_WHITE);
    display.display();
    delay(50);
  }
}
```

---

## Kegiatan 6 - Animasi Kombinasi (Gerak + Teks + Grafik)
Menampilkan teks dan bola bergerak secara bersamaan untuk efek dinamis.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
}

void loop() {
  for (int x = 0; x < 128; x += 2) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(SSD1306_WHITE);
    display.setCursor(10, 5);
    display.print("Animasi OLED");
    display.fillCircle(x, 45, 4, SSD1306_WHITE);
    display.display();
    delay(25);
  }
}
```

---

## Variasi Kegiatan Pengayaan (coba menggunakan promt AI semisal chatgpt, gemini, dsb)
- Menambahkan efek kedipan (*blinking*) atau *fade-in/fade-out* teks  
- Menampilkan animasi arah panah untuk menunjukkan navigasi seperti kompas
- Membuat jam digital animasi dengan efek transisi angka (counter up atau counter down) 
- Membuat animasi grafik sinyal sinusoid 3 fasa. 

---

# JOBSHEET 7
**Sistem Kontrol Motor DC Berbasis Intensitas Cahaya menggunakan Arduino (Motor Geared 2-kabel)**

---

## A. Tujuan Pembelajaran
Setelah praktikum ini mahasiswa mampu:
- Membaca dan menampilkan nilai intensitas cahaya pada OLED
- Mengontrol ON/OFF motor DC berbasis sensor LDR
- Mengatur kecepatan dan arah motor DC (motor geared 2-kabel) menggunakan driver L298N
- Memvisualisasikan grafik kecepatan motor terhadap waktu secara real-time pada OLED

---

## B. Alat dan Bahan
- Arduino Uno / kompatibel (1 unit)  
- Motor DC geared 2-kabel (1 unit)  
- Driver motor L298N (1 unit)  
- Sensor LDR + resistor 10 kΩ (1 set)  
- OLED I2C SSD1306 0.96" (1 unit)  
- Breadboard dan kabel jumper  
- Power supply motor 5–12 V (sesuaikan spesifikasi motor)  
- Power USB atau 5V untuk Arduino (dapat dipakai sebagai ground common)  

---

## C. Catatan Keamanan & Persiapan
- Pastikan ground (GND) power motor dan Arduino **disambung bersama** (common ground)  
- Jangan memberi suplai motor melebihi rating motor/geared; cek spesifikasi pabrikan  
- Saat melakukan wiring, pastikan semua kabel terpasang dengan rapi untuk menghindari short

---

## D. Konfigurasi Pin 
- LDR → A0  
- OLED SDA → A4, SCL → A5 (I2C)  
- L298N IN1 → D9  
- L298N IN2 → D10  
- L298N ENA (PWM) → D6  
- L298N OUT1/OUT2 → Motor DC (dua kabel motor)  
- L298N Vcc (motor supply) → Power supply 5–12 V  
- L298N GND → Ground bersama (Arduino GND + motor supply GND)

---

## E. Rangkaian 
> Semua koneksi diuraikan dengan format: **Komponen pin → Tujuan pin**

### Rangkaian Umum (digunakan pada semua kegiatan)
1. **Sensor LDR (dengan pembagi tegangan)**  
   - LDR satu sisi → 5V  
   - LDR sisi lain → titik pembagi → sambung ke A0 (nilai analog)  
   - Titik pembagi juga terhubung ke resistor 10kΩ ke GND  
   *(Alternatif: LDR dan 10k sebagai pembagi tegangan: 5V—LDR—A0—10k—GND)*

2. **OLED I2C**  
   - OLED SDA → Arduino A4  
   - OLED SCL → Arduino A5  
   - OLED VCC → Arduino 5V (atau 3.3V jika modul membutuhkan)  
   - OLED GND → Arduino GND

3. **Driver L298N dan Motor Geared (2-kabel)**  
   - Arduino D9 (IN1) → L298N IN1  
   - Arduino D10 (IN2) → L298N IN2  
   - Arduino D6 (ENA/PWM) → L298N ENA (untuk channel yang terhubung pada motor)  
   - L298N OUT1 → Motor kabel A  
   - L298N OUT2 → Motor kabel B  
   - L298N +V (Vs) → power supply motor (5–12 V)  
   - L298N GND → hubungkan ke GND power motor dan GND Arduino (common ground)  
   - Jika modul L298N memiliki pin 5V regulator (Vss) pilih sesuai instruksi modul; pastikan tegangan logic benar

4. **Catatan kabel**  
   - Gunakan kabel yang cocok untuk arus motor (jika arus > beberapa ratus mA gunakan kabel lebih tebal)  
   - Pastikan polaritas power motor benar saat pengujian pertama


---

## Kegiatan 1 — Monitoring Intensitas Cahaya pada OLED
**Tujuan:** Menampilkan nilai pembacaan LDR pada layar OLED secara real-time

**Wiring:**
- Susun pembagi tegangan: 5V → LDR → A0 → 10kΩ → GND  
- OLED SDA → A4, OLED SCL → A5, OLED VCC → 5V, OLED GND → GND  
- Arduino GND → (common) GND power

**Langkah Kerja:**
1. Rakitan rangkaian sesuai wiring di atas  
2. Upload program ke Arduino  
3. Amati nilai ADC pada OLED saat LDR diberi sumber cahaya atau ditutup

**Kode Arduino:**
```cpp
#include <Wire.h>
#include <Adafruit_SSD1306.h>

#define LDR_PIN A0
Adafruit_SSD1306 oled(128, 64, &Wire);

void setup() {
  oled.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  oled.clearDisplay();
  oled.setTextSize(2);
  oled.setTextColor(WHITE);
}

void loop() {
  int cahaya = analogRead(LDR_PIN);
  oled.clearDisplay();
  oled.setCursor(0,0);
  oled.print("Light:");
  oled.print(cahaya);
  oled.display();
  delay(200);
}
```

---

## Kegiatan 2 — Kontrol ON/OFF Motor DC Berdasarkan Intensitas Cahaya
**Tujuan:** Motor menyala ketika intensitas cahaya melebihi ambang, mati bila di bawah ambang

**Wiring:**
- L298N IN1 ← Arduino D9  
- L298N IN2 ← Arduino D10  
- L298N ENA ← Arduino D6 (untuk ON/OFF gunakan analogWrite 0 atau >0)  
- L298N OUT1/OUT2 → Motor (dua kabel)  
- Sambungkan semua GND (Arduino GND dan power motor GND)

**Langkah Kerja:**
1. Rakit semua koneksi sesuai wiring 
2. Tentukan ambang (threshold) nilai ADC; misal 500  
3. Upload kode dan uji dengan variasi cahaya

**Kode Arduino:**
```cpp
#define LDR_PIN A0
#define IN1 9
#define IN2 10
#define ENA 6

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);
}

void loop() {
  int cahaya = analogRead(LDR_PIN);
  int threshold = 500;

  if (cahaya > threshold) {
    // Motor ON (arah maju)
    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, 200); // duty 200/255
  } else {
    // Motor OFF
    analogWrite(ENA, 0);
  }
  delay(100);
}
```

**Pengamatan & Laporan:** Catat nilai cahaya saat motor berubah status; buat tabel pengamatan minimal 10 pembacaan

---

## Kegiatan 3 — Kontrol Kecepatan Motor Berdasarkan Besar-kecilnya Cahaya
**Tujuan:** Kecepatan motor meningkat seiring kenaikan intensitas cahaya

**Wiring:** Sama seperti Kegiatan 2

**Langkah Kerja:**
1. Rakit sesuai wiring 
2. Upload program yang mem-mapping nilai LDR ke PWM ENA  
3. Amati dan catat korelasi nilai ADC dan duty cycle

**Kode Arduino:**
```cpp
#define LDR_PIN A0
#define IN1 9
#define IN2 10
#define ENA 6

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);
  digitalWrite(IN1, HIGH); // arah default
  digitalWrite(IN2, LOW);
}

void loop() {
  int cahaya = analogRead(LDR_PIN);
  int pwm = map(cahaya, 0, 1023, 0, 255);
  analogWrite(ENA, pwm);
  delay(100);
}
```

**Pengukuran:** Buat tabel kecepatan (mis. dengan tachometer sederhana atau observasi dengan mengira-ngira cepat, sedang, atau pelan) vs nilai ADC

---

## Kegiatan 4 — Kontrol Arah Putar Motor Berdasarkan Kondisi Cahaya
**Tujuan:** Motor berubah arah saat kondisi cahaya melewati ambang tertentu

**Wiring:** Sama seperti Kegiatan 2

**Langkah Kerja:**
1. Rakit sesuai wiring  
2. Pilih ambang untuk perubahan arah (misal 500)  
3. Upload kode, uji dengan kondisi terang/gelap

**Kode Arduino:**
```cpp
#define LDR_PIN A0
#define IN1 9
#define IN2 10
#define ENA 6

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);
}

void loop() {
  int cahaya = analogRead(LDR_PIN);
  int threshold = 500;
  int pwm = 200;

  if (cahaya > threshold) {
    // Arah maju
    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
  } else {
    // Arah balik
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, HIGH);
  }
  analogWrite(ENA, pwm);
  delay(200);
}
```

**Catatan:** Saat mengubah arah, berikan jeda singkat (mis. hentikan motor selama 200–500 ms) sebelum membalik arah jika beban besar

---

## Kegiatan 5 — Plot Kecepatan Motor terhadap Waktu pada OLED (Realtime)
**Tujuan:** Menampilkan grafik scrolling kecepatan motor (berdasarkan PWM) pada OLED secara real-time

**Wiring:** Sama seperti Kegiatan 2 + OLED sudah terhubung ke I2C

**Langkah Kerja:**
1. Rakit semua koneksi sesuai wiring 
2. Upload kode plotting real-time berikut  
3. Amati grafik PWM / kecepatan pada OLED saat LDR disinari atau ditutup

**Kode Arduino (realtime plotting):**
```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define LDR_PIN A0
#define IN1 9
#define IN2 10
#define ENA 6

#define MAX_DATA 128
int dataPlot[MAX_DATA];
int idx = 0;

Adafruit_SSD1306 oled(128, 64, &Wire);

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);
  oled.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  for (int i = 0; i < MAX_DATA; i++) dataPlot[i] = 0;
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
}

void loop() {
  int cahaya = analogRead(LDR_PIN);
  int pwm = map(cahaya, 0, 1023, 0, 63); // 0-63 agar mudah diplot (tinggi pixel)
  analogWrite(ENA, pwm * 4);

  dataPlot[idx] = pwm;
  idx = (idx + 1) % MAX_DATA;

  oled.clearDisplay();
  // Gambar sumbu dasar
  oled.drawLine(0, 63, 127, 63, WHITE);

  // Gambar grafik scrolling (skala vertikal: 0..63 -> 63..0)
  for (int i = 0; i < MAX_DATA - 1; i++) {
    int x1 = i;
    int y1 = 63 - dataPlot[(idx + i) % MAX_DATA];
    int x2 = i + 1;
    int y2 = 63 - dataPlot[(idx + i + 1) % MAX_DATA];
    oled.drawLine(x1, y1, x2, y2, WHITE);
  }

  oled.setCursor(0,0);
  oled.setTextSize(1);
  oled.print("PWM:");
  oled.print(pwm * 4);
  oled.display();

  delay(50);
}
```

**Pengamatan:** Simpan screenshot nilai dan grafik (dengan kamera) untuk laporan

---

## G. Tugas dan Pelaporan
Mahasiswa wajib menyerahkan laporan yang memuat:
1. Foto rangkaian (gambar rakitan nyata)  
2. Tabel hasil pengamatan: nilai ADC LDR vs PWM/kecepatan motor (minimal 10 data)  
3. Screenshot / foto tampilan OLED berisi grafik real-time  
4. Analisis: hubungan intensitas cahaya dan perilaku motor, problem yang ditemukan, rekomendasi perbaikan  
5. Kesimpulan

---

## H. Lampiran — Troubleshooting Singkat
- **Motor tidak berputar:** Pastikan ENA diberi PWM > 0, Cek sambungan OUT1/OUT2 ke motor, pastikan supply motor ON  
- **Nilai LDR tidak berubah:** Periksa wiring pembagi tegangan, ganti resistor 10k jika rusak, cek kabel A0  
- **OLED blank:** Periksa alamat I2C (0x3C umum), periksa VCC/GND, pastikan library SSD1306 terinstal  
- **Motor panas berlebih:** kurangi supply voltage atau periksa beban mekanis

---


# Jobsheet 8 - Kontrol Motor Servo

## Seputar Motor Servo

### Kabel Standard Micro Servo

Motor servo memiliki tiga kabel utama yang berfungsi untuk catu daya dan kendali posisi. Pada umumnya, kabel cokelat berfungsi sebagai ground (GND), kabel merah sebagai tegangan positif (VCC) 5V, dan kabel oranye atau kuning sebagai sinyal kontrol (PWM) dari mikrokontroler seperti Arduino. Melalui sinyal PWM inilah posisi sudut poros servo diatur secara presisi.

![Kabel Motor Servo Standar](https://components101.com/sites/default/files/component_pin/Servo-Motor-Wires.png)

### Pinout Motor Servo Standard MG996R

Pada model MG996R, urutan dan fungsi pin tetap sama seperti servo standar, hanya saja jenis ini memiliki torsi yang lebih besar dan mampu menahan beban mekanis yang lebih berat, sehingga sering digunakan untuk aplikasi robotik, lengan mekanik, atau sistem kontrol industri kecil.

![Pinout Motor Servo MG996R](https://components101.com/sites/default/files/component_pin/MG996R-Servo-Motor-Pinout.png)


### Sudut dan PWM Motor Servo

Diagram yang menunjukkan hubungan antara lebar pulsa (PWM) dan posisi sudut motor servo. Hubungan antara lebar pulsa PWM dan posisi sudut servo ditunjukkan pada diagram. Pulsa dengan lebar sekitar 1 ms akan memutar servo ke posisi 0°, sedangkan 1,5 ms mengarah ke 90°, dan 2 ms menghasilkan posisi 180°. Arduino mengirimkan pulsa berulang dengan periode sekitar 20 ms untuk menjaga posisi servo tetap stabil.

![Servo Motor PWM Angle Range](https://www.circuitschools.com/wp-content/uploads/2022/02/servo-motor-pulse-width-modulation-angle-range.webp)

### Skema Rangkaian Servo dengan Arduino

Diagram skematik dasar untuk menghubungkan motor servo dengan Arduino. Pada skema rangkaian dengan Arduino, kabel VCC servo dihubungkan ke pin 5V Arduino, GND servo ke GND Arduino, dan kabel sinyal PWM dihubungkan ke salah satu pin digital PWM (misalnya pin 6). Disarankan untuk menggunakan sumber daya eksternal jika servo membutuhkan arus besar agar Arduino tidak mengalami gangguan daya.

![Interfacing Servo Motor with Arduino Circuit Diagram](https://www.circuitschools.com/wp-content/uploads/2022/02/interfacing-servo-motor-with-arduino-circuit-diagram.webp)

---
### Rekap Pinjam Alat dan Bahan


| Nama Alat / Bahan                     | Jumlah  | Keterangan / Digunakan pada Kegiatan |
| ------------------------------------- | ------- | ------------------------------------ |
| Arduino Uno                           | 1 unit  | Semua kegiatan                       |
| Motor Servo SG90 atau yang ada saja   | 1 unit  | Kegiatan 1–5                         |
| Sensor Ultrasonik HC-SR04             | 1 unit  | Kegiatan 2, 4, 5                     |
| Potensiometer 10 kΩ                   | 1 unit  | Kegiatan 1 dan 3                     |
| OLED 0.96" I²C Display                | 1 unit  | Kegiatan 3 (opsional di 5)           |
| Breadboard                            | 1 unit  | Semua kegiatan                       |
| Kabel Jumper (Male–Male, Male–Female) | ±10 pcs | Semua kegiatan                       |
| Kabel USB Arduino                     | 1 pcs   | Semua kegiatan                       |
| Buzzer                                | 1 unit  | Tantangan kegiatan 4 dan 5           |
| Arm servo dan selotip                 | 1 unit  | Kegiatan 4 (radar)                   |


### **Kegiatan 1 - Pengaturan Posisi Motor Servo Melalui Potensiometer**

#### **Tujuan**
Mengendalikan posisi sudut motor servo secara manual menggunakan potensiometer.

#### **Dasar Teori**
Motor servo merupakan aktuator yang mampu bergerak pada sudut tertentu (biasanya 0°--180°) sesuai sinyal PWM dari Arduino.\
Dengan menggunakan potensiometer, nilai analog yang dihasilkan (0--1023) dapat dikonversi menjadi sudut servo (0°--180°).


#### **Alat dan Bahan**
-   1 × Arduino Uno
-   1 × Motor servo (SG90)
-   1 × Potensiometer 10 kΩ
-   Breadboard dan kabel jumper
-   Kabel USB


#### **Skema Singkat**
-   Potensiometer:
    -   Kanan → 5V
    -   Tengah → A0
    -   Kiri → GND

-   Servo:
    -   VCC → 5V
    -   GND → GND
    -   Sinyal → Pin 6



#### **Kode Program Arduino**

```cpp
#include <Servo.h>

Servo myservo;
int potPin = A0;
int val;

void setup() {
  myservo.attach(6);
  Serial.begin(9600);
  Serial.println("Kontrol Servo dengan Potensiometer");
}

void loop() {
  val = analogRead(potPin);
  val = map(val, 0, 1023, 0, 180);
  myservo.write(val);
  Serial.print("Sudut Servo: ");
  Serial.println(val);
  delay(15);
}
```

#### **Cara Uji Coba**

Putar potensiometer perlahan dan amati perubahan sudut motor servo sesuai posisi potensio.

#### **Pertanyaan & Tantangan**

- Jelaskan hubungan antara nilai analog potensiometer dan sudut servo dalam kode Anda.
- Modifikasi program agar sudut servo bergerak secara halus (misalnya dengan menginterpolasi perubahan sudut).


* * * * *

### **Kegiatan 2 - Kontrol Motor Servo Berdasarkan Jarak dengan Sensor Ultrasonik**

#### **Tujuan**
Mengatur posisi servo berdasarkan jarak objek yang terdeteksi oleh sensor ultrasonik.


#### **Dasar Teori**
Sensor ultrasonik HC-SR04 mengukur jarak berdasarkan waktu pantulan gelombang suara. Nilai jarak ini dapat digunakan untuk mengatur posisi servo secara proporsional.


#### **Alat dan Bahan**
-   1 × Arduino Uno
-   1 × Sensor ultrasonik HC-SR04
-   1 × Motor servo
-   Breadboard dan jumper

#### **Skema Singkat**
-   Trig → Pin 9
-   Echo → Pin 10
-   Servo → Pin 6



#### **Kode Program Arduino**

```cpp
#include <Servo.h>

Servo myservo;
const int trigPin = 9;
const int echoPin = 10;
long duration;
float distance;
int angle;

void setup() {
  myservo.attach(6);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
  Serial.println("Kontrol Servo Berdasarkan Jarak");
}

void loop() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  angle = map(distance, 2, 20, 0, 180);
  angle = constrain(angle, 0, 180);
  myservo.write(angle);

  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.print(" cm | Sudut: ");
  Serial.println(angle);

  delay(200);
}
```



#### **Cara Uji Coba**

Dekatkan dan jauhkan tangan atau objek dari sensor, amati perubahan posisi servo sesuai jarak yang terukur.

#### **Pertanyaan & Tantangan**
- Bagaimana Anda menangani pengukuran jarak yang fluktuatif agar servo tidak ‘bergetar’?
- Tambahkan logika agar jika objek bergerak mendekat cepat, servo melakukan sweep (gerakan bolak-balik) sebagai alarm visual.


* * * * *

### **Kegiatan 3 - Tampilan Sudut Servo di Layar OLED**

#### **Tujuan**
Menampilkan nilai sudut servo secara real-time pada layar OLED 0.96".

#### **Dasar Teori**
Layar OLED dapat menampilkan informasi numerik dari Arduino menggunakan komunikasi I²C. Nilai sudut servo dapat diambil dari hasil pembacaan potensiometer atau sensor dan ditampilkan di layar.


#### **Alat dan Bahan**
-   1 × Arduino Uno
-   1 × OLED 0.96" I²C
-   1 × Motor servo
-   1 × Potensiometer 10 kΩ
-   Breadboard dan kabel jumper

#### **Skema Singkat**
-   OLED: VCC → 5V, GND → GND, SDA → A4, SCL → A5
-   Servo: Pin 6
-   Potensiometer: Tengah → A0

* * * * *

#### **Kode Program Arduino**

```cpp
#include <Servo.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

Servo myservo;
int potPin = A0;
int val;

void setup() {
  myservo.attach(6);
  Serial.begin(9600);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED tidak terdeteksi");
    for(;;);
  }
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
}

void loop() {
  val = analogRead(potPin);
  val = map(val, 0, 1023, 0, 180);
  myservo.write(val);

  display.clearDisplay();
  display.setCursor(0, 20);
  display.print("Sudut Servo:");
  display.setTextSize(2);
  display.setCursor(0, 35);
  display.print(val);
  display.display();

  delay(100);
}
```

#### **Cara Uji Coba**
Putar potensiometer dan amati perubahan nilai sudut servo yang ditampilkan di layar OLED secara real-time.

#### **Pertanyaan & Tantangan**
- Ubah tampilan OLED agar selain sudut juga menampilkan bar indikator (misalnya “▂▁▁▁▁” untuk 0° dan “▁▁▁▁▂” untuk 180°).
- Tambahkan fungsi “home” (servo kembali ke sudut 90° secara otomatis setiap 10 detik jika tidak ada input) dan tampilkan timer countdown di OLED.


* * * * *

### **Kegiatan 4 - Mini Radar Ultrasonik dengan Motor Servo**

#### **Tujuan**
Membuat sistem radar mini untuk mendeteksi objek di berbagai arah menggunakan kombinasi sensor ultrasonik dan servo motor.

#### **Dasar Teori**
Servo akan berputar dari 0° hingga 180°, sementara sensor ultrasonik mengukur jarak pada setiap sudut. Data jarak dapat ditampilkan di **Serial Monitor** atau **Serial Plotter** untuk divisualisasikan seperti radar.



#### **Alat dan Bahan**
-   1 × Arduino Uno
-   1 × Sensor ultrasonik HC-SR04
-   1 × Motor servo
-   Breadboard dan kabel jumper

#### **Skema Singkat**
-   Trig → Pin 9
-   Echo → Pin 10
-   Servo → Pin 6

#### **Kode Program Arduino**

```cpp
#include <Servo.h>

Servo myservo;
const int trigPin = 9;
const int echoPin = 10;
long duration;
float distance;

void setup() {
  Serial.begin(9600);
  myservo.attach(6);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.println("Mini Radar Aktif");
}

void loop() {
  for (int angle = 0; angle <= 180; angle += 5) {
    myservo.write(angle);
    delay(100);
    distance = getDistance();
    Serial.print(angle);
    Serial.print(",");
    Serial.println(distance);
  }

  for (int angle = 180; angle >= 0; angle -= 5) {
    myservo.write(angle);
    delay(100);
    distance = getDistance();
    Serial.print(angle);
    Serial.print(",");
    Serial.println(distance);
  }
}

float getDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2;
}
```

#### **Cara Uji Coba**
Tempelkan sensor ultrasonik di atas servo menggunakan selotip.\
Buka **Serial Plotter (Ctrl + Shift + L)** di Arduino IDE untuk melihat pola "radar" hasil scanning sudut 0--180°.

#### **Pertanyaan & Tantangan**
- Mengapa servo perlu berputar bolak-balik dari 0° hingga 180° saat melakukan pemindaian objek menggunakan sensor ultrasonik?
- Modifikasi program agar radar dapat mengaktifkan buzzer ketika mendeteksi objek pada jarak kurang dari 10 cm.

* * * * *

### **Kegiatan 5 - Sistem Sortir Barang Berdasarkan Ukuran atau Jarak**

#### **Tujuan**
Mensimulasikan sistem penyortiran barang berdasarkan ukuran objek menggunakan sensor ultrasonik dan motor servo.

#### **Dasar Teori**
Sensor ultrasonik diletakkan di atas jalur untuk membaca jarak antara sensor dan permukaan objek. Semakin besar objek, semakin dekat jaraknya ke sensor. Servo berfungsi untuk mengarahkan atau memindahkan barang ke posisi yang sesuai.

#### **Logika Penyortiran**

- Jika jarak < 5 cm  → objek besar  → servo ke 135°
- Jika jarak 5--10 cm → objek sedang → servo ke 90°
- Jika jarak > 10 cm → objek kecil  → servo ke 45°

#### **Alat dan Bahan**
-   1 × Arduino Uno
-   1 × Sensor ultrasonik HC-SR04
-   1 × Motor servo
-   Breadboard dan jumper

#### **Skema Singkat**
-   Trig → Pin 9
-   Echo → Pin 10
-   Servo → Pin 6


#### **Kode Program Arduino**

```cpp
#include <Servo.h>

const int trigPin = 9;
const int echoPin = 10;
long duration;
float distance;
Servo myservo;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  myservo.attach(6);
  myservo.write(90);
  Serial.println("Sistem Sortir Aktif");
}

void loop() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.println(" cm");

  if (distance < 5) {
    Serial.println("Objek besar terdeteksi");
    myservo.write(135);
  }
  else if (distance >= 5 && distance <= 10) {
    Serial.println("Objek sedang terdeteksi");
    myservo.write(90);
  }
  else {
    Serial.println("Objek kecil terdeteksi");
    myservo.write(45);
  }

  delay(500);
}
```

#### **Cara Uji Coba**

Letakkan sensor ultrasonik di atas jalur objek.\
Dekatkan objek dengan ukuran berbeda, dan amati pergerakan servo serta keluaran jarak pada **Serial Monitor**.


#### **Pertanyaan & Tantangan**
- Mengapa semakin besar ukuran objek, jarak yang terbaca oleh sensor ultrasonik menjadi semakin kecil?
- Modifikasi program agar sistem mengeluarkan bunyi buzzer ketika mendeteksi objek besar (jarak < 5 cm). Tambahkan juga tampilan kategori objek pada OLED jika tersedia.

## Jangan Lupa Buat Analisis dan Laporannya. 
