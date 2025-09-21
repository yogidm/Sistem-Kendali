# Praktikum Sistem Kendali - Arduino Mega 2560

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
  - LDR â†’ 5V  
  - Resistor 10kΩ -> GND  
  - Titik tengah -> A0 Arduino Mega  
- LED:  
  - Anoda -> pin 9 Arduino Mega (PWM)  
  - Katoda -> resistor â†’ GND  

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
