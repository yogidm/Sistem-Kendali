# Praktikum Sistem Kendali - Arduino Mega 2560
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
- 3 buah resistor 220Î©  
- 1 buah Breadboard  
- 6 kabel jumper  
- 1 kabel USB  
- Aplikasi **CoolTerm** untuk logging data

---

### C. Dasar Teori  
Sensor **LM35** adalah sensor suhu analog yang menghasilkan tegangan keluaran sebanding dengan suhu dalam skala Celsius.  
Setiap perubahan **10 mV** mewakili **1Â°C**.  
Arduino membaca nilai analog (0â€“1023) dari LM35 pada rentang tegangan 0â€“5V, sehingga suhu dapat dihitung dengan rumus:  

\[
\text{Suhu (Â°C)} = \frac{\text{Nilai ADC} \times 5.0 \times 100}{1024}
\]

---

## KEGIATAN 1 â€“ Sistem Open Loop (Pembacaan Sensor Saja)

### Tujuan  
Membaca suhu dari sensor LM35 tanpa memengaruhi output (monitoring saja).

### Langkah Percobaan  
1. Hubungkan pin LM35 sebagai berikut:  
   - VCC â†’ 5V Arduino  
   - OUT â†’ A0 Arduino  
   - GND â†’ GND Arduino  
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
  Serial.println(" Â°C");
  delay(1000);
}
```

### Analisis  
Amati hasil pembacaan suhu pada **Serial Monitor**. Perhatikan bahwa output tidak berubah meskipun suhu naik atau turun.

---

## KEGIATAN 2 â€“ Sistem Close Loop (Kontrol 3 LED Berdasarkan Suhu)

### Tujuan  
Menyalakan LED sesuai dengan batas suhu tertentu.

### Langkah Percobaan  
1. Hubungkan tiga LED dengan resistor ke pin berikut:  
   - LED Hijau â†’ pin 8  
   - LED Kuning â†’ pin 9  
   - LED Merah â†’ pin 10  
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
  Serial.println(" Â°C");
  delay(1000);
}
```

### Analisis  
Amati perubahan LED saat suhu sensor meningkat atau menurun.

---

## KEGIATAN 3 â€“ Kontrol Proporsional Warna LED

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

  // Mapping proporsional antara 20Â°C â€“ 40Â°C
  int intensitasHijau = map(suhu, 20, 40, 255, 0);
  int intensitasMerah = map(suhu, 20, 40, 0, 255);
  int intensitasKuning = 128;

  analogWrite(ledHijau, constrain(intensitasHijau, 0, 255));
  analogWrite(ledKuning, intensitasKuning);
  analogWrite(ledMerah, constrain(intensitasMerah, 0, 255));

  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.println(" Â°C");
  delay(500);
}
```

### Analisis  
Amati transisi warna LED yang berubah perlahan seiring perubahan suhu.

---

## KEGIATAN 4 â€“ Logging Data Suhu ke File CSV

### Tujuan  
Merekam data suhu Arduino ke komputer dan memplot grafik di Microsoft Excel.

### Langkah Percobaan  
1. Unduh dan pasang aplikasi **CoolTerm** dari situs resmi:  
   [https://freeware.the-meiers.org](https://freeware.the-meiers.org)  
2. Jalankan CoolTerm, pilih **Connection â†’ Options â†’ Serial Port**, atur:  
   - Port: COM Arduino Anda  
   - Baudrate: 9600  
   - Data Bits: 8, Stop Bits: 1, Parity: None  
3. Klik **Connect**. Data suhu akan tampil secara real-time.  
4. Untuk menyimpan data, pilih **Connection â†’ Capture to Textfile â†’ Start**.  
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
  Serial.println(" Â°C");
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



## Jangan Lupa Buat Analisis dan Laporannya. 
