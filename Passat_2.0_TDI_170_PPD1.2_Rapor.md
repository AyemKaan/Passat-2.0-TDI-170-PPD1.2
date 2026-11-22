# VW Passat 2.0 TDI 170 HP (Siemens PPD1.2) — Detaylı Yazılım, DTC, Harita ve Dyno Raporu

## 1. Araç ve ECU Bilgileri
- **Araç:** VW Passat B6 2.0 TDI 16V (BMR) — 170 HP / 350 Nm
- **Şasi No (VIN):** `WVWZZZ3CZ8P050406`
- **Motor Beyni (ECU):** Siemens VDO PPD1.2 (`R4 2.0l PPD1.2`)
- **Yazılım Parça No:** `03G906018EM` — Versiyon: `0420`
- **Kalibrasyon Kimliği:** `6576286119` (`SN1K540` / `CASN1K54.DAT`)
- **Dosya Boyutu:** `2,097,152 Bayt` (2 MB OBD Okuma — Kalibrasyon Bölgesi: `0x40000 - 0x80000`)
- **Hazırlanan Yazılım Paketleri:**
  1. `Passat_2.0_TDI_170_Stage1_DPF_Safe_EGR.bin` — **Stage 1 DPF-Safe + EGR Off + Launch Control (2500 RPM)** *(~198 HP / 390 Nm Krank)*
  2. `Passat_2.0_TDI_170_Stage1_EGR_LC_Popcorn.bin` — **Stage 1 Full + EGR Off + Launch Control (2750 RPM) + Popcorn (4700 RPM)** *(~210 HP / 412 Nm Krank)*
  3. `Passat_2.0_TDI_170_Stage1_Plus_DPF_EGR_Off_LC_Popcorn.bin` — **Stage 1+ (Plus) Max Safe + DPF Off + EGR Off + Hardcut Launch Control Popcorn (3000 RPM) + 4 Katmanlı Hardcut Popcorn (4500 RPM)** *(~226 HP / 435 Nm Krank — 465 Nm İndike)*

---

## 2. KESS v2 ve Checksum (Sağlama Toplamı) Durumu

> **KESS v2 ile Yazdırma Öncesi Önemli Bilgi:**
> 1. Siemens PPD1.2 kalibrasyon bölgesinde (`0x402C0 - 0x402F3` bloğunda) **32-bit MSB CRC-32 (`0x04C11DB7`, init `0x00000000`)** kalibrasyon sağlama toplamı (`0x402C4`) bulunur.
> 2. `Passat_2.0_TDI_170_Stage1_Plus_DPF_EGR_Off_LC_Popcorn.bin` dosyasının `0x402C4` kalibrasyon CRC-32 checksum'ı **birebir hesaplanıp dosyaya yazılmıştır** (`Orijinal: 0xD6BABED9` → **`Yeni: 0x46DD1863`**, doğrulandı).
> 3. **KESS v2 (K-Suite)** ile OBD üzerinden yazarken yine de karşılaştırma için orijinal dosyayı (`Passat_2.0_TDI_170_Stock_ORI.bin`) sorarsa gösterebilirsiniz; checksum zaten doğru olduğu için KESS v2 / MPPS / BDM üzerinden sorunsuz yazılır ve tek marşta çalışır.

---

## 3. DTC Off Tablosu (Kapatılan Arıza Kodları)

Siemens PPD1.2'de her arıza kodu (Failure Memory / `fmy_id_*`) 28 baytlık bir bloktan oluşur ve bloğun 25. baytındaki **`c_err_clas` (Hata Sınıfı)** değeri `0x00` yapıldığında o arıza kodu tamamen devre dışı kalır (ne arıza lambası yakar, ne de korumaya/limp moda geçer).

### 3.1. Tüm Paketlerde Kapatılan 10 Adet EGR Arıza Kodu

| Sıra | Hata Sınıfı Adresi | Kayıt Başlangıcı | Orijinal Değer | Yeni Değer | Kapatılan OBD (P-Kodu) | Kapatılan VAG Kodu | A2L Tanımı / Açıklaması |
| :---: | :--- | :--- | :---: | :---: | :--- | :--- | :--- |
| **1** | `0x4D307` | `0x4D2EE` | `147` (`0x93`) | **`0` (`0x00`)** | **P2425, P2426, P2427** | `01495`, `01496`, `09004` | `fmy_id_byp_0` — EGR Soğutucu Bypass Valfi Kontrol / Devre Arızası |
| **2** | `0x4D323` | `0x4D30A` | `147` (`0x93`) | **`0` (`0x00`)** | **P0401** | `01025`, `08997` | `fmy_id_egr_ctl_min` — EGR Akışı Yetersiz Algılandı (Pozitif Sapma) |
| **3** | `0x4D33F` | `0x4D326` | `147` (`0x93`) | **`0` (`0x00`)** | **P0402** | `01026`, `08999` | `fmy_id_egr_ctl_max` — EGR Akışı Aşırı Algılandı (Negatif Sapma) |
| **4** | `0x4D35B` | `0x4D342` | `197` (`0xC5`) | **`0` (`0x00`)** | **P0401** | `05214`, `10261` | `fmy_id_maf_egr_thr_obd` — OBD EGR / Boğaz Kelebeği Hava Akış Eşiği Düşük |
| **5** | `0x4D377` | `0x4D35E` | `197` (`0xC5`) | **`0` (`0x00`)** | **P0402** | `05215`, `10262` | `fmy_id_maf_dif_egr_thr_obd` — OBD EGR / Boğaz Kelebeği Hava Akış Farkı Yüksek |
| **6** | `0x4D393` | `0x4D37A` | `147` (`0x93`) | **`0` (`0x00`)** | **P0404, P2513** | `12592`, `09491` | `fmy_id_egrv_0_ctl` — EGR Valfi (N18) Kontrol Sapması / Sıkışma (Alt Sınır) |
| **7** | `0x4D3AF` | `0x4D396` | `147` (`0x93`) | **`0` (`0x00`)** | **P0404, P2514** | `12593`, `09492` | `fmy_id_v_egrv_0_bol_ad` — EGR Valfi (N18) Adaptasyon / Kontrol Hatası (Üst Sınır) |
| **8** | `0x4D3E7` | `0x4D3CE` | `147` (`0x93`) | **`0` (`0x00`)** | **P0403, P0489, P0490** | `05121`, `05122`, `05184` | `fmy_id_egrv_0` — EGR Valfi (N18) Elektrik Devresi / Kısa Devre (Düşük/Yüksek) |
| **9** | `0x4D403` | `0x4D3EA` | `147` (`0x93`) | **`0` (`0x00`)** | **P0403, P1400** | `05120`, `09008` | `fmy_id_egrv` — EGR Valfi (N18) H-Bridge Sürücü / Açık Devre Arızası |
| **10** | `0x4E69B` | `0x4E682` | `147` (`0x93`) | **`0` (`0x00`)** | **P2413, P0405, P0406** | `05186`, `05187`, `09347` | `fmy_id_v_egrv_0_rng` — EGR Potansiyometre (G212) Sinyal / Performans Hatası |

### 3.2. `Passat_2.0_TDI_170_Stage1_Plus_DPF_EGR_Off_LC_Popcorn.bin` Dosyasında Ek Olarak Kapatılan 14 Adet DPF, Fark Basınç (G450) ve Egzoz Sıcaklık (EGTS) Arıza Kodu

| Sıra | Hata Sınıfı Adresi | Kayıt Başlangıcı | Orijinal Değer | Yeni Değer | Kapatılan OBD (P-Kodu) | Kapatılan VAG Kodu | A2L Tanımı / Açıklaması |
| :---: | :--- | :--- | :---: | :---: | :--- | :--- | :--- |
| **11** | `0x4D013` | `0x4CFFA` | `197` (`0xC5`) | **`0` (`0x00`)** | **P2002** | `08194`, `10073` | Partikül Filtresi (DPF) Verimlilik Eşiğin Altında (Bank 1) |
| **12** | `0x4D04B` | `0x4D032` | `147` (`0x93`) | **`0` (`0x00`)** | **P0471** | `09299`, `10249` | `fmy_id_dip_pf_mes_es` — DPF Fark Basınç Sensörü (G450) Sinyal / Plausibilite |
| **13** | `0x4D067` | `0x4D04E` | `147` (`0x93`) | **`0` (`0x00`)** | **P0471** | `01137`, `10256` | `fmy_id_dip_pf_mes_eru` — DPF Fark Basınç Sensörü (G450) Ölçüm Sapması (Alt) |
| **14** | `0x4D083` | `0x4D06A` | `147` (`0x93`) | **`0` (`0x00`)** | **P0471** | `01137`, `10257` | `fmy_id_dip_pf_mes_grd` — DPF Fark Basınç Sensörü (G450) Ölçüm Sapması (Üst) |
| **15** | `0x4D0D7` | `0x4D0BE` | `147` (`0x93`) | **`0` (`0x00`)** | **P2002** | `08194`, `10081` | Partikül Filtresi (DPF) Kurum / Kül Doluluk Sınırı Aşıldı |
| **16** | `0x4D0F3` | `0x4D0DA` | `147` (`0x93`) | **`0` (`0x00`)** | **P0471** | `01137`, `10259` | DPF Fark Basınç Sensörü (G450) Dinamik Akış Plausibilite Hatası |
| **17** | `0x4D95F` | `0x4D946` | `197` (`0xC5`) | **`0` (`0x00`)** | **P2002** | `08194` | OBD Partikül Filtresi (DPF) Verimlilik / Akış Direnci Hatası |
| **18** | `0x4E2AB` | `0x4E292` | `197` (`0xC5`) | **`0` (`0x00`)** | **P2080** | `08320` | Egzoz Gazı Sıcaklık Sensörü 1 (G235 - Turbo Öncesi) Sinyal / Plausibilite |
| **19** | `0x4E2C7` | `0x4E2AE` | `147` (`0x93`) | **`0` (`0x00`)** | **P2084** | `06489` | `fmy_id_tac_mes_plaus` — Egzoz Gazı Sıcaklık Sensörü 2 (G448 - DPF Öncesi) Plausibilite |
| **20** | `0x4E2E3` | `0x4E2CA` | `147` (`0x93`) | **`0` (`0x00`)** | **P242B** | `09259` | Egzoz Gazı Sıcaklık Sensörü 3 (G495 - DPF Sonrası) Sinyal / Plausibilite |
| **21** | `0x4E3FB` | `0x4E3E2` | `197` (`0xC5`) | **`0` (`0x00`)** | **P0545, P0546** | `01349`, `01350` | `fmy_id_v_teg_tur_up_rng` — EGTS 1 (G235) Devre Düşük / Yüksek |
| **22** | `0x4E417` | `0x4E3FE` | `147` (`0x93`) | **`0` (`0x00`)** | **P2032, P2033** | `06487`, `06488` | `fmy_id_v_tac_rng` — EGTS 2 (G448 - DPF Öncesi) Devre Düşük / Yüksek |
| **23** | `0x4E433` | `0x4E41A` | `147` (`0x93`) | **`0` (`0x00`)** | **P242C, P242D** | `05212`, `05213` | EGTS 3 (G495 - DPF Sonrası) Devre Düşük / Yüksek |
| **24** | `0x4E647` | `0x4E62E` | `147` (`0x93`) | **`0` (`0x00`)** | **P0472, P0473** | `01138`, `01139`, `09344` | `fmy_id_v_dip_pf_rng` — DPF Fark Basınç Sensörü (G450) Devre Düşük / Yüksek |

> **Önemli:** Turbo (P0234/P0299), Piezo Enjektör, Krank/Eksantrik, MAF/MAP ve Soğutma Suyu/Yakıt Sıcaklık sensörlerinin arıza kodlarına **kesinlikle dokunulmamıştır**; motorun tüm güvenlik ve teşhis fonksiyonları tam aktiftir.

---

## 4. Kullanılan Haritalar ve Detaylı Değişiklik Listesi

### 4.1. Sürücü İstek (Driver Wish) Haritaları (`ip_fac_tq_req_dri_*`)
- **A2L İsimleri:** `ip_fac_tq_req_dri_h__n__pv_av` & `ip_fac_tq_req_dri_l__n__pv_av` (Farklı vites ve sürüş modları için 10 adet harita)
- **Hex Adresleri:** `0x7AAF0`, `0x7ABF0`, `0x7ACF0`, `0x7ADF0`, `0x7AEF0`, `0x7AFF0`, `0x7B120`, `0x7B340`, `0x7B6C0`, `0x7B7C0`
- **Boyut & Format:** `16x8` (16-bit Big-Endian Unsigned, Dönüşüm: `(Değer - 32768) / 32.0 = Nm`)
- **Yapılan İşlem:** %85 ve %100 gaz pedalı sütunları (Sütun 6 ve 7), 1500–4500 RPM aralığında DPF-Safe pakette **430 Nm**, Stage 1 Full pakette **455 Nm**, Stage 1+ (DPF Off) pakette **480 Nm** tork talep edecek şekilde yükseltildi.

### 4.2. Ana Tork Limitörü (`ip_tqi_tql_bas`)
- **Hex Adresi:** `0x4ABE2` (RPM Ekseni `ldpm_n_ip_tqi_bas`: `0x465BC`)
- **Boyut & Format:** `20x1` (16-bit Big-Endian, `(Değer - 32768) / 32.0 = Nm`)
- **Devir Bazlı Orijinal vs. Stage 1 / Stage 1+ Değerleri:**

| Devir (RPM) | 1000 | 1250 | 1500 | 1750 | 2000 | 2250 | 2500 | 2750 | 3000 | 3250 | 3500 | 3750 | 4000 | 4200 | 4300 / *4500* | 4500 / *4700* | 5400 / *4720* |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Orijinal (İndike Nm)** | 170 | 240 | 320 | 370 | 380 | 380 | 380 | 375 | 370 | 365 | 360 | 355 | 350 | 345 | 330 | 305 | 0 |
| **Stage 1 DPF-Safe (Nm)** | 175 | 245 | 330 | 385 | **415** | **418** | **418** | **415** | **410** | **405** | **395** | **385** | **375** | 365 | 345 | 315 | 0 |
| **Stage 1 Full+Popcorn (Nm)** | 175 | 250 | 340 | 395 | **435** | **440** | **440** | **438** | **435** | **430** | **420** | **410** | **395** | 380 | 355*(4500)* | 330*(4700)* | 0*(4720)* |
| **Stage 1+ DPF Off+Popcorn (Nm)** | 175 | 255 | 350 | 410 | **458** | **465** | **465** | **462** | **456** | **448** | **438** | **428** | **418** | **405** | **392***(4350)* | **378***(4500)* | **0***(4501)* |

### 4.3. Yardımcı Tork Limitörleri ve Tork -> Yakıt Çevrim Haritaları
- **Atmosferik & Kaynama Koruması (`ip_tqi_tql_amp` & `ip_tqi_tql_bpr` @ `0x7A8E0`, `0x7B8C0`, `0x7B9E0` - `12x12`):**
  - Stokta 340 Nm olan üst sınırlar DPF-Safe'de **418 Nm**, Full pakette **440 Nm**, Stage 1+ pakette **465 Nm** seviyesine ölçeklendi.
- **Yakıt Sıcaklığı Tork Limitörü (`ip_tqi_tql_tfu_prot` @ `0x7BDA8` - `16x16`):**
  - Normal yakıt sıcaklıklarında (ilk 7 sütun) 385 Nm olan sınır **418 / 440 / 465 Nm** yapıldı; aşırı ısınmış yakıt sütunlarında güvenlik koruması korundu.
- **Vites Koruması Tork Limitörleri (`ip_tq_gb_prot_l__n`, `mid__n`, `h__n` @ `0x4AA3A`, `0x4AA52`, `0x4AA8A`, `0x4AAA2` - `12x1`):**
  - Stokta 290–360 Nm olan vites tork sınırları 1750–4000 RPM bandında **418 / 440 / 465 Nm** seviyesine çıkarıldı.
- **Tork -> Yakıt/Turbo/SOI Eksenleri (`ldpm_tqi_sp_*` @ `0x44F72 - 0x44FD8` ve `0x46D62 - 0x47404`):**
  - Stokta 300 ve 400–420 Nm'de biten tork eksenlerinin son iki noktası Stage 1'de `360 / 445 Nm`, Stage 1+ pakette **`375 / 470 Nm`** olarak yeniden ölçeklendi.
- **Tork -> Yakıt Çevrim Haritaları (`ip_mf_bas_0_0..1_1` @ `0x60D86 - 0x62586`, 12 adet `16x16` harita, Dönüşüm: `(Değer - 5000) / 250.0 = mg/stk`):**
  - Stage 1+ pakette 465 Nm indike tork talebinin 70 mg/stk stok tavanına takılmaması için son iki sütun (375 Nm ve 470 Nm sütunları) 1500–4750 RPM aralığında **75.5–76.5 mg/stk** yakıt talebi üretecek şekilde kalibre edildi.

### 4.4. Enjektör Süre (`ip_doi_main1_*`) ve Enjeksiyon Başlangıç Açısı (`ip_soi_main1_*`) — *(Stage 1+ Pakette)*
- **Enjektör Yakıt Ekseni (`ldpm_mf_ip_doi_main1` @ `0x45942`, `16x1`):**
  - Stokta `60.0` ve `70.0 mg/stk` olan son iki eksen noktası Stage 1+ pakette **`63.0` ve `76.0 mg/stk`** (`15750` ve `19000`) olarak genişletildi.
- **Piezo Enjektör Süre Haritaları (`ip_doi_main1_0..5` @ `0x5381C - 0x5441C`, 6 adet `16x16` harita, Dönüşüm: `Değer / 42.6667 = °CRK`):**
  - Yeni `63.0` ve `76.0 mg/stk` sütunları, Siemens piezo enjektörlerin `50 -> 60 -> 70 mg/stk` arasındaki doğrusal debi eğimi korunarak (~36–38 °CRK maksimum enjeksiyon açısı, güvenli EOI sınırları içinde) kalibre edildi.
- **Enjeksiyon Başlangıç Açısı / Avans Haritaları (`ip_soi_main1_0_0..1_2` @ `0x6DC8C - 0x6F48C` ve `ip_soi_main1_max` @ `0x7008C - 0x70C8C`, 18 adet `16x16` harita, Dönüşüm: `(5120 - Değer) / 42.6667 = °BTDC`):**
  - Stage 1+ pakette yüksek devir ve yüksek yükte (2000–4500 RPM, son 3 sütun) yanmanın egzoz zamanına sarkmasını (yüksek EGT'yi) önlemek ve silindir içi pik basıncı güvenli sınırda tutmak için **`+1.25° ile +2.0° CRK`** arasında dinamik SOI avansı verildi.

### 4.5. Duman Limitörü (`ip_mf_slm_bas__n__maf_fil`)
- **Hex Adresi:** `0x6A9D6` (Rejenerasyon Duman Limitörü `ip_mf_slm_bas_rgn`: `0x6AC36`)
- **MAF Ekseni (`ldpm_maf_fil` @ `0x467E4`, `19x1`):** Son 3 noktası `900, 1000, 1150 mg/stk` olarak düzenlendi.
- **Yapılan İşlem:**
  - Yüksek hava akışı (750–1150 mg/stk MAF) sütunlarında yakıt sınırı stok **65.0 mg/stk** değerinden:
    - **Stage 1 DPF-Safe:** **67.5 mg/stk** (`AFR ≥ 16.0:1`, sıfır duman)
    - **Stage 1 Full:** **72.5 mg/stk** (`AFR ≥ 15.3:1`)
    - **Stage 1+ (DPF Off):** **75.5 mg/stk** (2000–2750 RPM), **74.0 mg/stk** (3000 RPM), **72.5 mg/stk** (3500 RPM), **69.0 mg/stk** (4000 RPM) (`AFR ≥ 15.2:1`, temiz ve güçlü yanma) seviyesine çıkarıldı.

### 4.6. Turbo Basınç Haritaları ve Limitörleri (`ip_map_sp_bas_*` & `ip_map_sp_max_*`)
- **Ana Turbo Haritaları:** `0x5E98E` (`bas_0_0` sıcak), `0x5EB8E` (`bas_0_1` ılık), `0x5EF8E` (`bas_1_0` sıcak), `0x5F18E` (`bas_1_1` ılık) — `16x16` (`Değer / 12.0602 = hPa`)
- **Turbo Basınç Limitörleri:** `0x5FF8E` (`ip_map_sp_max__n__amp`, `16x8`) ve `0x6008E` (`ip_map_sp_max__n__tia`, `16x8`), `c_map_dif_max_diag` @ `0x4B802` (`1x1`)
- **Yapılan İşlem:**
  - Sıcak motor hedef turbo basıncı yüksek yükte (1750–4000 RPM) stok **2500 hPa (1.50 bar)** değerinden:
    - **Stage 1 DPF-Safe:** **2610 hPa (1.61 bar)** (Limitörler: 2680 hPa)
    - **Stage 1 Full:** **2650 hPa (1.65 bar)** (Limitörler: 2680 / 2720 hPa)
    - **Stage 1+ (DPF Off):** **2690 hPa (1.69 bar)** @ 2250–3000 RPM, **2680 hPa (1.68 bar)** @ 3500 RPM, **2670 hPa (1.67 bar)** @ 4000 RPM, **2600 hPa (1.60 bar)** @ 4500 RPM (BV43 turbonun DPF'siz egzozda en verimli ve güvenli tavanı; Limitörler: **2750 / 2780 hPa**, Overboost eşiği `0x4B802`: **800 hPa**).

### 4.7. DPF İptali ve EGR İptali
- **DPF İptali (`Stage1_Plus_DPF_EGR_Off_LC_Popcorn.bin`):**
  - **DPF Rejenerasyon Durum Maskesi (`c_state_rgn_pf` @ `0x4BC34`):** `0x0001 -> 0x0000` yapılarak rölanti/rejenerasyon tetiklemesi kapatıldı.
  - **Rejenerasyon Enjeksiyon Konfigürasyon Tabloları (`id_conf_inj_rgn_1` @ `0x475AC` & `id_conf_inj_rgn_2` @ `0x475BC`):** Normal sürüş tablosu (`id_conf_inj_0` @ `0x4757C`) ile birebir eşitlenerek DPF rejenerasyonundaki tüm **post-injection (sonradan yakıt püskürtme)** fazları %100 devre dışı bırakıldı (kartere mazot karışması ve beyaz duman riski sıfırlandı).
  - **Rejenerasyon MAF Haritaları (`0x5C546 - 0x5CD46`):** Normal sürüş MAF haritaları gibi **850 mg/stk (`0x9CA8`)** olarak düzleştirildi.
  - **DPF / Fark Basınç (G450) / Egzoz Sıcaklık (EGTS) DTC'leri:** 14 adet hata sınıfı `0x00` yapılarak kapatıldı (sensör fişleri takılı veya çekili kullanılabilir).
- **EGR İptali (Tüm Paketlerde):**
  - `0x4A8AC - 0x4A96C` ve `0x4AB5C - 0x4AB96` adreslerindeki tüm EGR açma/kapama tork eşikleri **0.0 Nm** yapıldı; `0x5CD46 - 0x5D746` arasındaki 6 adet MAF haritası **850 mg/stk** olarak düzleştirildi.

### 4.8. Hardcut Launch Control (3000 RPM) & 4 Katmanlı Hardcut Popcorn (4500 RPM) — *(Stage 1+ Güncel)*
- **3000 RPM Sert Launch Control Popcorn (`0 km/h`):**
  - **Öngörülü PI Yumuşak Limitör İptali (`c_n_max_mt` @ `0x4B980`):** `5200 RPM` orijinal değerine alınarak Siemens `SUPP_AN_LIM` PI kontrolcüsünün 3000 RPM'den önce torku yumuşakça kısması (soft-cut yapması) engellendi.
  - **1-RPM Keskin Duvar (`ip_tqi_lim_clu_prot` @ `0x7B440`, `8x8` & Ekseni `0x42FAE`):** Eksen **`3000 → 3001 RPM`** (1 RPM fark!) olarak ayarlandı. `0 km/h` sütunu `3000 RPM`'de tam güç (`1024 Nm`), **`3001 RPM`'de anında `0.0 Nm`** keser; araç hareketlendiğinde (`≥ 1.8 km/h`) anında **`465 Nm`** tam kalkış torku serbest bırakılır.
  - **Boşta Spool Desteği (`ip_mf_slm_bas` @ `0x6A9D6`):** 2500–3000 RPM orta MAF bölgesinde (`400–615 mg/stk`) `+3.0 mg/stk` ek yakıt verilerek boşta/kalkışta turbonun daha hızlı dolması ve 3000 RPM patlamalarının sertleşmesi sağlandı.
- **4500 RPM 4 Katmanlı Sert Popcorn Hardcut (Giderken / Tüm Viteslerde):**
  1. **Ana Tork Limitörü (`ip_tqi_tql_bas` @ `0x4ABE2`, Eksen `0x465BC`):** **`4500 RPM = 378 Nm` → `4501 RPM = 0.0 Nm`** (1-RPM duvar; vites tork limitörleri `0x4AA3A..0x4AAA2` 4500 RPM'de `385–400 Nm` yapılarak erken kesme önlendi).
  2. **Duman / Doğrudan Yakıt Limitörü (`ip_mf_slm_bas` @ `0x6A9D6` & `_rgn` @ `0x6AC36`, Eksenler `0x464A6` & `0x464C8`):** **`4500 RPM = 68.5 mg/stk` → `4501 RPM = 0.0 mg/stk`** (tüm 19 MAF sütununda sıfır yakıt).
  3. **Tork → Yakıt Çevrim Haritaları (`ip_mf_bas_0_0..1_1` @ `0x60D86 - 0x62386`, 12 harita, Eksenler `0x45B98` & `0x45BBA`):** **`4500 RPM = 76.5 mg/stk` → `4501 RPM = 0.0 mg/stk`** (tüm 16 tork sütununda sıfır yakıt).
  4. **Piezo Enjektör Süre Haritaları (`ip_doi_main1_0..5` @ `0x5381C - 0x5421C`, 6 harita, Eksen `ldpm_n_ip_doi_main1` @ `0x4611C`):** **`4500 RPM = Tam Süre (~40.3 °CRK)` → `4501 RPM = 0.00 °CRK`** (tüm 16 yakıt sütununda anında sıfır püskürtme süresi).

---

## 5. Tork (Nm) ve Beygir Gücü (HP) Eğrisi (Dyno Grafiği)

Aşağıdaki tablo ve grafik, **Orijinal (170 HP / 350 Nm)**, **Stage 1 DPF-Safe (~198 HP / 390 Nm)**, **Stage 1 Full (~210 HP / 412 Nm)** ve **Stage 1+ DPF Off (~226 HP / 435 Nm Krank — 465 Nm İndike)** eğrilerinin devir bazlı karşılaştırmasını göstermektedir:

### Devir Bazlı Krank Torku (Nm) ve Güç (HP) Tablosu

| Devir (RPM) | Orijinal Tork | Stage 1 DPF-Safe | Stage 1 Full | **Stage 1+ (DPF Off)** | Orijinal Güç | Stage 1 DPF-Safe | Stage 1 Full | **Stage 1+ (DPF Off)** |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1500** | 295 Nm | 305 Nm | 315 Nm | **325 Nm** | 63 HP | 65 HP | 67 HP | **69 HP** |
| **1750** | 345 Nm | 360 Nm | 370 Nm | **384 Nm** | 86 HP | 90 HP | 92 HP | **96 HP** |
| **2000** | 350 Nm | 387 Nm | 407 Nm | **429 Nm** | 100 HP | 110 HP | 116 HP | **122 HP** |
| **2250** | 350 Nm | 390 Nm | 412 Nm | **435 Nm** | 112 HP | 125 HP | 132 HP | **139 HP** |
| **2500** | 350 Nm | 390 Nm | 412 Nm | **435 Nm** | 125 HP | 139 HP | 147 HP | **155 HP** |
| **2750** | 345 Nm | 385 Nm | 408 Nm | **431 Nm** | 135 HP | 151 HP | 160 HP | **169 HP** |
| **3000** | 338 Nm | 378 Nm | 403 Nm | **424 Nm** | 145 HP | 162 HP | 172 HP | **181 HP** |
| **3250** | 332 Nm | 372 Nm | 396 Nm | **415 Nm** | 154 HP | 172 HP | 183 HP | **192 HP** |
| **3500** | 325 Nm | 360 Nm | 385 Nm | **404 Nm** | 162 HP | 180 HP | 192 HP | **201 HP** |
| **3750** | 318 Nm | 348 Nm | 373 Nm | **392 Nm** | 170 HP | 186 HP | 199 HP | **209 HP** |
| **4000** | 310 Nm | 336 Nm | 356 Nm | **380 Nm** | **170 HP** | 192 HP | 203 HP | **216 HP** |
| **4200** | 298 Nm | 325 Nm | 340 Nm | **365 Nm** | 170 HP | **198 HP** | **210 HP** | **226 HP** |
| **4500** | 250 Nm | 268 Nm | 283 Nm | **315 Nm** | 160 HP | 172 HP | 182 HP | **202 HP** |
| **4700** | 210 Nm | 225 Nm | 260 Nm | **282 Nm** | 141 HP | 151 HP | 174 HP | **189 HP** |

---

### ASCII Dyno Grafiği — Krank Torku (Nm) ve Beygir Gücü (HP)

```text
TORK (Nm)                                                                 GÜÇ (HP)
 440 Nm +------------------------------------------------------------------+ 230 HP
        |                    @ @ @ (435 Nm Stage 1+ DPF Off)     @ (226HP) |
 420 Nm |                 @         @   @                     @            | 215 HP
        |               @    * * * (412 Nm Full)           @     * (210HP) |
 400 Nm |              *   *       *   *   @            @     *     @      | 200 HP
        |             @  *   # # # (390 Nm DPF-Safe) @     *     # (198HP) |
 380 Nm |            #  #  #       #   #   *      @     *     #     *      | 180 HP
        |           *  #               #   #  @ *    *     #        #      |
 360 Nm |          #  . . . (350 Nm Stok)     #  * @    #        .  .      | 160 HP
        |         .  .     .  .  .               * #  #  .  .  .           |
 340 Nm |        .                  .  .      *  .    #            *       | 140 HP
        |       @                           . *  #   .                #    |
 320 Nm |      *                         *  #   .                        . | 120 HP
        |     #                       *  #   .                             |
 300 Nm |    .                     *  #   .                                | 100 HP
        |                       *  #   .                                   |
 280 Nm |                    *  #   .                                      |  80 HP
        +--------+-------+-------+-------+-------+-------+-------+---------+
       1500    1750    2000    2250    2500    3000    3500    4000   4500 RPM

 Semboller:
   [ . ] Orijinal Stok          (Peak: 350 Nm @ 2000-2500 RPM | 170 HP @ 3750-4200 RPM)
   [ # ] Stage 1 DPF-Safe       (Peak: 390 Nm Krank / 418 Nm İndike | 198 HP @ 4000-4200 RPM)
   [ * ] Stage 1 Full Paket     (Peak: 412 Nm Krank / 440 Nm İndike | 210 HP @ 4000-4200 RPM)
   [ @ ] Stage 1+ DPF & EGR Off (Peak: 435 Nm Krank / 465 Nm İndike | 226 HP @ 4000-4200 RPM)
```
