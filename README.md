# IFondasi
Analisis kapasitas dukung dan penurunan pondasi dangkal

# Fitur

Berikut beberapa fitur yang dapat digunakan yaitu:
1. Tinjau pengaruh variasi lebar atau kedalaman fondasi.
2. Penurunan dan kontur penurunan (dengan bantuan modul *ISondir*)
2. Kapasitas dukung *Effectif Stress Analysis (ESA)* dan *Total Stress Analysis (ESA)*
3. Kapasitas dukung geser umum (*general shear*) dan geser lokal (*local shear*)
4. Faktor kapasitas dukung, dengan metode:
    - Davis and Booker (*default*)
    - Meyerhof
    - Vesic
5. Bentuk pondasi persegi (*rectangular*) atau lingkaran (*circular*)
6. Kapasitas dukung dan penurunan dari data sondir

# Contoh
Beikut contoh perintah minimum dalam IFondasi:
```
import IFondasi

p1 = IFondasi.Shallow(name="Footing",
                      Df = 1.0, # meter
                      B = 1.2, # meter
                      L = 1.2, # meter
                      shape = "rectangular")
p1.soil_ESA(
    GWL = 1, # meter
    phi = 30, # derajat
    g = 18, # kN/m3
    method = "Davis and Booker",
    local_shear = False)
    
p1.loads(Vn=500,H=0,M=0)
p1.analyze(FS=3)

p1.checkSuitability()
p1.info()
```
Result
```
qu = 661.0 kPa
qult = 679.0 kPa
qa = 238.0 kPa
Pa = 343.0 kN
```
Kontrol kapasitas,
```
p1.suitability
```
Result
```
'Vn = 500 kN > Pa = 343.0 kN; (Unecceptable)'
```
# Example 02: Storage Tank 2500 kL
Data tanah diperoleh dengan korelasi data sondir. Kemudian dilakukan perhitungan kapasitas dukung dan penurunan pondasi.

```
import ISondir

s1 = ISondir.Single(id_="S1")
s1.read_excel("raw_data.xlsx")
s1.setGWL(z=1)
s1.solve_basic()
s1.solve_parameters()
```

```
from IFondasi import Shallow

tank = Shallow(name="Storage Tank",B=17.5,Df=1.0,shape="circular")
tank.soil_ESA(CPT=s1) # << input data sondir
tank.loads(Vn=26020) # kN
tank.analyze(FS=2.5)
```

### Kontur penurunan dengan metode Janbu
```
tank.settl_contour()
```

![settlement contour](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmc4IRChrbs6YXDLYZSSQ2ePa1Lj-PEm2jA_CgZVYH7l-4LgRZB5r-XUZsLIGntXOhlVDNo-WWW8TcwUAw849WVJKEABXAa5qM10YO7DFQ4aiQ5q5rjyrZTvDw16-q1lyzyErTqmCB4C5Yc8AhfX-Mhqlro3sisL7KXAaH4JPvhQjvqUD23o_BGeZghw/w640-h400/settl_contour.png)

```
tank.qa_varwidth(
width=[2,18], # variasi lebar 2 m - 18 m
interval=0.5, # interval 0.5 m
settl_target=80 # cm, penurunan target
)
```
![bearing capacity](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVwHbocWWsrR5Pt1xU9xO4sTHzHHriGJgZ1jnBteEgq5UHWIU3c9690pWK3WEVji7JNpA8tEkRUOziYGqTBAkORa2eesZHeY4oVk5R2Q5ENFD1w6xegR3FKkk4YZT0w1qi4xi3nhPWU9nULtYTSrIifrRf-WG8uWycbkaKjN1uvAIq_cQI3vb9WGqtlQ/w640-h421/general_settl.png)
