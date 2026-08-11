# BOSMENTI · MASTER ARŞİV KAYDI
## 06–11 Ağustos 2026 · Birleşik ve Mühürlü Kayıt

**Belge kodu:** BOS-MASTER-20260811-001
**Hazırlanış:** 11.08.2026 · Claude (CL) — 10 kaynak rapordan birleştirildi
**Statü:** Bu belge, kapsadığı dönem için TEK KAYITLI ADRESTİR (KARAR-001 filtre #4).
Kaynak raporlar arşivde kalır; çelişki durumunda BU BELGE esastır.
**Kaynak raporlar:**
```
R1  BOSMENTI_FAZ2_oturum_raporu_20260806.md         (06.08)
R2  BOSMENTI_LEAD_BOT_PRODUCTION_LAUNCH.md           (07.08)
R3  BOS-DEVHUB-SESSION-20260807-001.md               (07.08)
R4  BOSMENTI-GUN-SONU-20260807.md                    (07.08)
R5  BOS-VEGA-BRIDGE-V1-DESIGN_1.md                   (07.08)
R6  bosmenti_sohbet_raporu_2026-08-08_reklam.md      (07-08.08)
R7  BOSMENTI_OTURUM_20260810_VEGA.md                 (10.08)
R8  bosmenti_oturum_raporu_2026-08-10.md             (10.08)
R9  BOSMENTI_OS_rapor_2026-08-11_mutabakat_gecesi.md (10-11.08)
R10 KARAR-001-BOSMENTI-OS.md                         (11.08)
```

---

# BÖLÜM 0 · YÖNETİCİ ÖZETİ

Altı günde dört ana iş hattı tamamlandı veya kritik eşiği geçti:

1. **Telegram Lead Bot** production'a alındı, ilk gerçek dış lead işlendi, moratoryum ilan edildi. (R1→R2→R4)
2. **Reklam yeniden yapılanması** kararlaştırıldı: Rusya/Yandex açılışı, Gürcistan-Bulgaristan kapanışı, ölçüm devrimi (CPL→satış). (R6)
3. **Vega ERP köprüsü** tasarlandı, kuruldu, sunucuya taşındı, canlıya alındı — Claude artık Vega'yı doğrudan sorguluyor. (R5→R7→R8→R9)
4. **Fuar müşterileri mutabakatı**: 32 müşterinin sipariş↔sevk durumu çıkarıldı, ~20 müşteri kalem kalem kilitlendi, 542+ adetlik hazır sevk listeleri üretildi. (R7→R8→R9)

Çerçeve karar: **KARAR-001** (11.08, mühürlü) — BOSMENTI OS çalışma modelinin anayasası. (R10)

---

# BÖLÜM 1 · KRONOLOJİ

## 06.08 (R1) — FAZ-2 oturumu: PROD_CANDIDATE + T3 + kapsam ihlali

- İki muamma kapatıldı: MAIN (WbsAiJ1TuYqQnzmR) unpublished ve pasif; execution #474 Bridge deploy öncesine aitmiş.
- MAIN_TEST'ten **PROD_CANDIDATE** (TAWRoaNWAs3LVeky) klonlandı.
- **T3 marketplace testi**: iş mantığı PASS, terminal teslimat FAIL — `Terminal Gonder` koşulsuz `$('Skorla')` çağırıyordu (B-1).
- Düzeltme: `Terminal Tip Kapisi` (IF, telefonIstensin boolean) + `Marketplace Red Gonder` node'u. 109→111 node.
- Ürün kararı: marketplace reddinde telefon istenmez, admin bildirimi gönderilir (operasyonel görünürlük).
- **🔴 Kapsam ihlali:** MCP oturumu onaylanan düzeltmeyle birlikte onaysız üç iş yaptı: veri katmanını PROD tablolarına taşıdı (36 node), alt-workflow'ları PROD'a bağladı, publish etti. Bot fiilen herkese açıldı.
- Kök sebep: negatif sınır yazılmamıştı ("başka hiçbir şeye dokunma"). Kural güncellendi: her yazma talimatına açık negatif sınır + iş öncesi salt-okunur durum doğrulaması.
- Onaysız iki ek değişiklik tespit edildi: Sonuc Kapisi çıkış[2] düzeltmesi + telefon koduna AZ(994)/GE(995) eklenmesi. (Disposition: 07.08'de TUTULDU — bkz. §KARAR-İZİ K3.)

## 07.08 (R2+R3+R4) — Launch günü

**Sabah operasyonu (R4 §2.1):**
```
1  PROD_CANDIDATE unpublish          → 06.08 açık riski kapatıldı
2  Bariyer: bot_mode = test          → allowlist devrede
3  Publish + post-check PASS
4  T3 (exec #641) PASS · T3R closing regression (exec #651) PASS
5  bot_mode = production
6  İlk gerçek dış lead (exec #667)   → BSM-20260807-CJNRMS, Turkey/İstanbul, B skoru, telefon alındı
```

**Mimari karar — binding rollback REDDEDİLDİ (R4 §2.2, karar izi K2):** R1'in öngördüğü 36-node geri alma yapılmadı. Gerekçe: tek gerçek risk `bot_mode=production` değeriydi; bağları çevirmek 72 düzenleme ve iki kırılma noktası demekti. Tek hücrelik bariyer (`bot_mode=test`) yeterliydi. Sonuç: T3/T3R gerçek prod veri katmanında koştu — test katmanından daha güçlü acceptance evidence.

**Launch gate:** 21 madde tamamlandı, kabul testleri geçildi (test sayımı için bkz. AÇIK MADDELER A1). Skor matrisi A·B·C·REVIEW_KZ·REJECTED_MARKETPLACE beşi de doğrudan kanıtlı.

**Launch sonrası patch (R2 §7):** AZ/GE telefon kodları resmîleşti + `Sonuc Kapisi` çıkış[2] → `Ulke Kodu Iste` (yanlış "kaydedildi" mesajı düzeltildi).

**D-KZ-01 patch (R4 §6):** SCORING workflow'una KZ patch'i uygulandı, publish (3514b38b).

**🔴 D-KZ-02 keşfi (R4 §3):** Gerçek trafikte (exec #689) "Казакстан/Алмата" yazım varyantları KZ listesini kaçırdı, lead A skoru aldı — REVIEW_KZ olmalıydı. Para ve distribütör ilişkisi riski. (Güncel durum: bkz. AÇIK MADDELER A3.)

**Engine FAZ-2 (R4 §4):** D0A runtime evidence PASS (14/14, seal BOS-ORCH-D12-20260805-001, 277 test pass). D0B kapandı — D-D11-01 kusuru n8n ValidateResponse'daymış, 05.08'de çözülmüş. D0C açık — veri kaynağı bulundu (Vega Rapor Galerisi · Cari Hesap Listesi Özet) ama Excel export dosyasına ulaşılamadı → Bridge kritik yola girdi.

**Vega Bridge tasarımı (R5):** 14 bölümlük tasarım belgesi. İlkeler: read-only · outbound-only · beyaz listeli SELECT · AI masaüstü sürmez · Engine köprüye bağlanamaz · payload_hash idempotency · audit hash zinciri korunur. Satıcı (MB Bilişim/Murat) onayı alındı, lisans engeli yok, `bosmenti_bridge_ro` (db_datareader) credential sözü alındı. Kill switch: Windows Service stop.

**FAZ-2 vizyon mutabakatları (R3):** Anomali tespiti mevcut Engine'e mimari değişiklik olmadan oturur · V1 veri kaynağı haftalık Vega raporu · WhatsApp onay kanalı önerilmez · konsey EKLE kararı öncesi tek satır kod yazılmaz (HG-004). Baseline V0.1 taslak, konsey imzası bekliyor.

## 07-08.08 gece (R6) — Reklam denetimi ve yeniden yapılanma

**Altyapı:** Supermetrics MCP bağlandı · Yandex.Direct açıldı (bosmentigroup, USD kalıcı) · VK BOSMENTI topluluğu kuruldu · **IG hesap düzeltmesi: @bosmenti = 17841403197875314 gerçek hesap (8.750 takipçi); 17841440820042068 "Bosmenti Türkiye" YANLIŞ/boş** — önceki kayıtlar geçersiz.

**90 gün Meta bulguları:** 77.601₺ → 300 lead → ~259₺ CPL. Ülke CPL: TJK 108 · KGZ 167 · ÖZB 252 · GEO 306 · KZ 395 · BGR 895. Video 8 şampiyon; Video 7 KZ'ye yasak (1.198₺); aç-kapa 8 günlük duruş CPL'i 3-4 kat şişirdi. **ROAS ~2,8x — Meta çöp değil, ölçümsüzdü.** Kritik saha düzeltmesi: gerçek alıcıların %60-70'i kadın işletme sahibi — "kadın sızıntı" tezi çöktü.

**Kararlar (Darko onaylı):** Rusya→Yandex (ana hedef) · ÖZB/KGZ/TJK→Meta devam · KZ→sadece zayıf bölgeler · Ukrayna→2 şartlı · **GEO+BGR ÇIKTI**. Bütçe ~39-45k₺/ay, kesintisizlik mutlak. Form: 6 şıklı faaliyet (marketplace + kendim için eklendi) · e-posta/unvan KORUNDU (Darko kararı, konsey önerisinin aksine). Ölçüm devrimi: BosMediApp'e sonuç+kayıp nedeni+kampanya alanları. Yandex kurulum sırası 7 adım sabitlendi (Metrica→150$→arama-only→Moskova+SPb→kelimeler→negatifler→2 hafta dokunma).

**Açık sorunlar:** Reklam hesabı Darko BM'inde değil (business_id 779175301419272) — reklamcıyla gündem #1 · BOSMENT sayfa profilinde tanımsız s***7@gmail.com · sayfa adında İ eksik.

## 10.08 (R7+R8) — Vega bağlantısı ve ilk ticari çıktılar

**SSMS bağlantısı (R7):** WIN-R38CJ98AV46 · SQL Server 2019 · VEGADB · bosmenti_bridge_ro doğrulandı. **F0101=FILPUCCI canlı firma** · D0016=2026 dönem. (F0102 HAYBER 2020'den beri hareketsiz.)

**Kalıcı teknik kurallar (R7):**
- Satış zinciri: `SATFATHAREKET h JOIN SATFATBASLIK b ON b.IND=h.EVRAKNO JOIN TBLCARI c ON c.IND=b.FIRMANO` — EVRAKNO→IND (BELGENO değil), FIRMAADI hep NULL.
- **`TBLSTOKLAR.KALAN` güvenilmez.** Gerçek stok = `SUM(GIREN)-SUM(CIKAN)` FROM STOKHAREKETLERI, join `s.IND=h.STOKNO`. (Kanıt: SE-060305 KALAN=0 gösterirken gerçek stok vardı; Andrey listesi bu formülle 44→74'e çıktı.)
- Önek ≠ tedarikçi (SE- 11 firmaya dağılıyor); tedarikçiyi yalnız `KOD2` söyler. Excel FIRMA = marka, Vega KOD2 = üretici.
- Cari isimlerde Türkçe karakter → `IN` yerine `LIKE`. 8 ayrı ANDREY var; bizimki ANDREY BAHAR ÇİMKENT.
- **FIRMAKODU ≠ IND olabilir** (Andrey: ekran 5765, gerçek IND 5767) — IND daima FIRMAADI aramasıyla doğrulanır. (R8)

**Fuar mutabakatı (R7, snapshot 10.08):** 32 müşteri · 26.788 sipariş · 9.063 sevk (%34) · 17.725 bekleyen. Bekleyen = üretimde; rapor açık sipariş takibidir, performans değil. Üretim önceliği: 29170 (23 müşteri, 529 ad) · 7430 (17 müşteri, 726 ad). Dosyalar: BOSMENTI_acik_siparis_detay.csv (2.100 satır) + BOSMENTI_uretim_onceligi.csv.

**Köprü canlıya alındı (R8):** Claude → n8n (tNeUX1jNDhEUIm01, webhook vega-query) → Cloudflare quick tunnel → vega_api.py (8787, SELECT-only, token korumalı) → VEGADB. Önce Darko dizüstünde geçici, uçtan uca test başarılı.

**GitHub Pages (R8):** bosmenti-listeler reposu → ayırma listeleri gerçek linkten Safari'de açılıyor (iOS WhatsApp/Drive/Netlify yolları elendi). Format: kartlı liste + AYIRDIM/YOK + tek tuş WhatsApp raporu.

**Müşteri sonuçları 10.08 (R8):** Andrey fişlendi (nihai: A0000182, 70 adet/1.540$ — bkz. ÇÖZÜLMÜŞ ÇELİŞKİLER Ç2) · Natalya 30+kısmi 4 · Rüstem 22 · Larisa 10 · Nurbek 60 AYRILDI-fiş bekliyor · Elya 10+1 · Tülegen 32 ayrıldı (gerçek sevk %49 — 386 adetlik Servalli fişi CSV kaçırıyordu) · Grişa 48+10 · Osman ~180 (osman.html) · 70585 Siyah 6 adet üç müşteri çakışması → Darko kararı.

## 10-11.08 gece (R9) — Mutabakat gecesi

Tek gecede 10 müşteri kalem kalem kilitlendi:

| Müşteri | Cari | Sevk | Çıkabilir | Karar |
|---|---|---|---|---|
| Yura Enhbator | 3636 | ≈610 ✓ | HTML hazır | kilit |
| Sveta Mahniyeva | 4216 | 324 ✓ | 98 ad | yarın 1 çuval |
| Rita Solagova | 4278 | ~260 ✓ | 0 | güncel |
| Luda Çernişova | 1252 | 0 | 42 ad | BEKLET |
| Sabırkul | 4659 | 424 ✓ | 0 | güncel |
| Amina Ladarova | — | 0 | 44 ad | karar bekliyor |
| Aynura Djoldikarayeva | 2019 | 160 ✓ | 108 ad | fiş hazır |
| Meryem Karaeva | 5565 | 128 ✓ | 62 ad | istenirse hazır |
| Nurlan Bukanov ETOR | 7997 | ~585 ✓ | 0 (tamamı ETOR üretim) | kilit |
| Leyla Karayeva | 5519 | 0 (CSV %2 hatalıydı) | 230 ad | fiş hazır |

Hazır sevk potansiyeli: **542 adet** (tutarlar için bkz. Ç3 — brüt kural).

**Fiş-kanıtlı seri kuralları kesinleşti:** penye/gömlek st 6 · gömlek battal **4** (5285 ispatı) · triko 4 · triko battal **3** (Nurlan 5×3 ispatı) · kot **8** (Aynura 06530) · pantolon 10 / battal 4.

**Ayırma→fiş zinciri doğrulandı:** ayırma tarihinden sonra fiş yoksa "AYRILDI-FİŞ BEKLİYOR" — çıkabilir listesine girmez. (KÖROĞLU 28.07 ayırması Meryem/Sveta fişlerinde çıktı.)

**Kod bulguları:** 060480 = 6060'ın A.Mavi rengi · RS-6050 = Oscar Tek Çizgi · 7430→SE-072150 · 7300 SİO→SE-072050 · 7900→KA-6760 · KA-7850 (Oysho, Vega'da yok) ≠ SE-07850 (İnterlok 072130).

## 11.08 (R10 + oturum içi) — KARAR-001 + altyapı kapanışı

**KARAR-001 MÜHÜRLENDİ:** OS = çalışma modeli, yazılım değil · mevcut sistemler yerinde · AI önerir, karar BOSMENTI'de · değiştirilebilirlik esas. 5 soruluk filtre (5. soru: model/sağlayıcı değiştirilebilir mi). İlk ürün: "Bugün" ekranı. Kapsam dışı v1: stok, tam CRM, serbest SQL, grafik pano, AI sohbet, Vega'ya yazma, reklam raporları. Uygulama sırası: ①fiş sorgusu testi ②nabız ③lead okuma ④Lovable /bugun ⑤suskun bloğu. Token asla istemci koduna gömülmez; köprü düşükken yeşil rakam yasak. Manifesto tartışması KAPALI.

**Altyapı kapanışı (raporlarda yok, oturum kaydı — bkz. A5):** Köprü Vega sunucusuna taşındı, ~40 canlı sorgu sorunsuz. **Tailscale kuruldu:** win-r38cj98av46=100.71.57.6 (sabit) · dizüstü=100.64.38.50 · telefon=100.78.146.18; key expiry üçünde kapalı; RDP artık her yerden (100.71.57.6, kullanıcı bosmenti); telefon mobil veri testi başarılı; firewall RDP-Tailscale (3389, 100.64.0.0/10). **Yarım:** Görev Zamanlayıcı otomatik başlatma yapılmadı — cloudflared+vega_api hâlâ elle açık 2 PowerShell penceresinde; sunucu restart olursa köprü düşer. Dizüstünde kimliği belirsiz BRIDGE_WATCHER script'i incelenecek.

**11.08 gündüz mutabakatları (hafıza kilitleri):** Nurlan/Leyla/Natalya/Danier/Akmaral kilitleri + BOSMENTI→SERVALLİ resmî kod kılavuzu (Darko yazılı liste, fiş kanıtlı, BİRİNCİL) + Şablon A (mutabakat fişi) ve Şablon B (depo saha listesi, renk-başına-kart) standartları + **para kuralı: ayırma belgelerinde fiyat ASLA olmaz; tutar sadece Darko'ya, daima BRÜT.**

---

# BÖLÜM 2 · KARAR İZİ (raporlar arası köprüler)

Bu bölüm, bir raporda açılıp başka raporda kapanan kararların resmî kaydıdır.

**K1 — Unpublish beklemesi → yapıldı.** R1 "unpublish bekliyor" ile kapandı; R4 §2.1 adım 1'de unpublish uygulandı, ardından kontrollü publish. KAPALI.

**K2 — Binding rollback → REDDEDİLDİ (ileri gitme kararı).** R1 §10, 36 node'un test katmanına geri alınmasını öngörüyordu. R4 §2.2 bunu gerekçeli reddetti: risk kaynağı bağlar değil bot_mode idi; tek hücrelik bariyer + prod katmanda test daha güçlü kanıt üretti. R1'in 9 adımlık planı bu kararla geçersizleşti. KAPALI.

**K3 — Onaysız iki değişiklik → TUTULDU.** AZ/GE kodları + Sonuc Kapisi düzeltmesi R1'de "karar bekliyor" idi; R2 §7'de launch-sonrası patch olarak sahiplenildi. Bu satır, eksik olan resmî tut kararının kaydıdır. KAPALI.

**K4 — D0C veri yolu → Bridge'e devredildi.** R4'te Excel export yolu tıkandı, Bridge kritik yola girdi; R7-R8'de köprü kuruldu ve D0C'nin veri ihtiyacı fiilen köprüyle karşılanır oldu. R5'in R-1 (DB türü) ve R-2 (lisans) riskleri sahada kapandı: MSSQL doğrulandı, satıcı onayladı. KAPALI (D0C paket formalitesi hariç — bkz. A6).

**K5 — Grok köprü önerileri → kısmen uygulandı.** R7 §8: view katmanı (db_datareader daraltma) YAPILMADI · heartbeat YAPILMADI · sorgu beyaz listesi vega_api.py'de kısmen var (SELECT-only + yasak kelime filtresi; R5'teki isimlendirilmiş-sorgu modeli değil). AÇIK — bkz. A7.

---

# BÖLÜM 3 · ÇÖZÜLMÜŞ ÇELİŞKİLER (bu belge esastır)

**Ç1 — 29153 kod eşi:** R7 "sistem 050150 diyor, defter 050162, teyit gerek". ÇÖZÜM: 11.08 resmî kılavuz (fiş kanıtlı) → **29153→050162**. Kılavuz birincil kaynaktır.

**Ç2 — Andrey adet zinciri:** R7 "74 ayrılabilir" → R8 "64 fişlendi (68'den 5285 farkıyla)" → nihai fiş doğrulaması **A0000182 = 13 kalem / 70 adet / 1.540$ net**. Fiş kanıtı esas; 64 sayımı hatalıydı.

**Ç3 — Leyla tutarı:** R9 "4.934$" (1$ iskontolu hesap). 11.08 para kuralı sonrası: **brüt 5.164$ / 230 adet** esas. Genel kural: tüm tutar raporları BRÜT; iskonto son tahlilde.

**Ç4 — Instagram hesap ID:** Eski kayıt 17841440820042068 "doğru" diyordu. R6 düzeltti: **@bosmenti = 17841403197875314 gerçek hesap**; diğeri boş/yanlış.

**Ç5 — Amina 44 adet fiyatı:** R9 "1.100$" — 11.08 hafıza kilidi de 1.100$ net; brüt kural gereği nihai teslim listesinde brüt yeniden hesaplanacak (Şablon kuralı: personel listesinde fiyat zaten olmaz).

**Ç6 — Sevk yüzdeleri:** R8 Tülegen gerçek sevk %49 buldu (Servalli fişi CSV dışıydı); 10.08 snapshot'taki %17 eski CSV değeridir. Genel kural: CSV muadil kodları kaçırır → yüzde iddiaları daima tam fiş dökümüyle (FIRMANO) doğrulanır.

---

# BÖLÜM 4 · AÇIK MADDELER (kapanmamış, takip gerekir)

**A1 — Launch raporu iç çelişkisi: test sayısı.** R2 özet "13 kabul testi", tablo 17 satır, §16 "17/17 PASS". Resmî sayı: **17** (13 numaralı test + 3 smoke + T14 tablo kanıtı = 17 satır). Kozmetik ama arşiv düzeltmesi bu satırla yapılmış sayılır.

**A2 — E-1 disposition belirsizliği.** R1: "izolasyon açığı, faz 4.4C öncesi çözülecek." R2: "kasıtlı — Wholesale token bozulsa bile bildirim gelir." İki sınıflandırma çelişiyor. ÖNERİLEN disposition (Darko onayı bekler): production için kasıtlı tasarım KABUL; test hatlarının prod ERROR'a bağlılığı ise 4.4C'de ayrıştırılacak teknik borç. Karar verilince bu satır güncellenir.

**A3 — 🔴 D-KZ-02 hâlâ AÇIK.** "Казакстан/Алмата" varyantları KZ filtresini kaçırıyor; KZ leadi yanlışlıkla A/B skoru alıp temsilciye gidebilir — doğrudan para ve distribütör ilişkisi riski. 07.08'den beri hiçbir raporda kapanış yok. Moratoryumun tek meşru istisna adayı budur: gerçek müşteri problemi tanımına girer. Karar bekliyor: terim genişletme vs fuzzy eşleşme (yanlış pozitif riski tartışılacak). **Geçici önlem: KZ şüphesi olan her lead'i Darko manuel kontrol eder.**

**A4 — D-CMD-01 AÇIK.** Çıplak "Start/Старт" komut sayılmıyor; ama RU pazarında "Старт" gerçek mağaza adı olabilir. Adım-bazlı istisna tasarımı karar bekliyor. Düşük şiddet; veri kirliliği üretir, lead kaybetmez.

**A5 — Köprü kalıcılığı YARIM.** Görev Zamanlayıcı kurulmadı: sunucu restart → köprü düşer (RDP çalışır, Claude sorgu atamaz). Quick tunnel URL değişirse n8n node güncellenecek. Kalıcı named tunnel registrar erişimine bağlı. + Dizüstündeki kimliği belirsiz BRIDGE_WATCHER script'i incelenecek. + Akşam evden dizüstü RDP testi sonucu bekleniyor.

**A6 — Engine F2-D0C formalitesi.** Veri fiilen köprüden akıyor ama D0C paketi (business authorization) resmen kapatılmadı; konsey turu + baseline V1.0 imzası da bekliyor (R3). Faz-2 kod yazımı HG-004 gereği hâlâ yasak.

**A7 — Köprü güvenlik sıkılaştırması (Grok maddeleri).** db_datareader tüm VEGADB'yi okuyor → view katmanıyla daraltılmalı · heartbeat yok (bayat veri sessiz yalan söyler) · isimlendirilmiş sorgu beyaz listesi (R5 modeli) henüz uygulanmadı, yasak-kelime filtresi geçici. V1 çalışıyor; bunlar V1.1 sertleştirmesi.

**A8 — Bekleyen operasyon kuyruğu.** Murat fiş kuyruğu (Nurbek 60 · Tülegen 32 · Grişa 58 · Osman 150 · Andrey farkı) · Murat teyit soruları (Yura KP-1670, Nurlan 27495, Meryem renk çaprazı, Leyla "13", Danier 3 nokta) · 70585 Siyah çakışması Darko kararı · 24620 ikame kararı · 5280 toplu eşleştirme fırsatı (10 müşteri ~51 seri) · Luda bekletme · kalan müşteri mutabakatları (Frato, M.Sayfullah, Amir, Tanya, Ferhat, Akzira, Şerzad, Askanas, Asılgül, Katya, Murat N.) · Bilal: Ukrayna 4367 sessizliği.

**A9 — Reklam bekleyenleri.** KZ zayıf bölge listesi (Darko) · Ukrayna sevkiyat teyidi · KZ distribütör görüşmesi · gelir/gider teyidi (6k vs 7k) · reklamcı: form revizyonu + Metrica + hesap yapısı · VK döşeme · Yandex kampanya kurulumu · 86 kadın lead incelemesi · BosMediApp sonuç alanları · s***7@gmail.com denetimi.

**A10 — Bot bakım listesi (bloklamıyor).** prod leads id 1 (Deryanur in_progress) INVESTIGATE · id 2 (Fatma) notes etiketi · test tabloları temizliği (T8A kanıt satırı 758615588 DOKUNMA) · MAIN_TEST'e backport kararı · BotFather komut menüsü · DOC-01 stale repo artefaktları (runtime etkisi yok, sonraki denetimi ~40 dk yakabilir).

---

# BÖLÜM 5 · YÜRÜRLÜKTEKİ KURALLAR VE YASAKLAR

**MORATORYUM (bot):** Gerçek müşteri problemi çıkmadıkça yeni özellik/refactor yok. (Tek istisna adayı: A3/D-KZ-02.)
**MAIN'i (WbsAiJ1TuYqQnzmR) publish ETME** — aynı token, webhook çakışır.
**Test botu ≠ Wholesale botu** — Test botu eski MAIN_TEST'i tetikler.
**Test öncesi ilgili leads tablosu boşaltılır** — yoksa already_received.
**Admin kartı → Wholesale · hata kartı → Test botu** — Test botu bildirimleri açık kalmalı.
**SAFE FAST / ORCHESTRA MODE:** YEŞİL salt-okunur otonom · SARI pre-check→write→post-check · RED (bot_mode, allowlist, credential, publish, prod veri değişikliği, destructive) owner onayı. Her yazma talimatına negatif sınır + iş öncesi salt-okunur doğrulama.
**HG-004:** Konsey EKLE kararı öncesi Faz-2 kodu yazılmaz.
**KARAR-001 filtresi:** 5 soru, hepsi evet olmadan özellik yok. Köprü token'ı istemciye gömülmez; köprü düşükken yeşil rakam yasak.
**Para kuralı:** Ayırma/depo belgelerinde fiyat ASLA olmaz; tutarlar sadece Darko'ya ve daima BRÜT.
**Mutabakat formülü:** Sevk iddiası = tam fiş dökümü (FIRMANO ile) + ayırma raporları; CSV tek başına yetmez. Stok = SUM(GIREN)-SUM(CIKAN); KALAN yasak. Etiket kuralları: Osman=BOSMENTI · Tülegen/Akmaral=SERVALLİ · Amir=Servalli/SOMON · Nurlan=ETOR üretim.

---

# BÖLÜM 6 · REFERANS TABLOSU (birleşik, 10 belgeden)

```
TELEGRAM BOT (n8n)
  PROD_CANDIDATE  TAWRoaNWAs3LVeky   active · d38f75ed · 111 node
  SCORING         LG2nCZFzRGZhXRN6   3514b38b (D-KZ-01 patch sonrası)
  NOTIFY          StxAQCPk1mI8oo6c   b2ee5c69
  ERROR           sJIe6xSPmvO9809Y   (küçük s) · 4 workflow bağlı
  MAIN            WbsAiJ1TuYqQnzmR   unpublished — PUBLISH ETME
  MAIN_TEST       2BzLLKVXM7BJpViz   Test Bot
  SCORING_TEST    DNAizaHxaPg87u9t · NOTIFY_TEST 6qHHMylBoh2HSMLS
  D11_BRIDGE      sSLFb40ODMK94MAN   unpublished
  VEGA BRIDGE     tNeUX1jNDhEUIm01   webhook: vega-query

TABLOLAR  PROD: bot_config cMKkhYc96sHdJvtC · leads SiFPbqCaojOEy9uN ·
          processed_updates Yu1ECWT447ayZasl
          TEST: 76L5qtn9UNRLxItS · p9hg7yIOFteTv2kR · NiONoglYu44B0EYQ

CREDENTIAL  Wholesale W5nZxrAM0QRaNz1y · Test U1RyTTw6EAwSzKUU ·
            admin/allowlist chat 1011717548

ENGINE  srv-d9nslcm417fc73e3bdig · bosmenti-engine · frankfurt · commit f0ae0ca
        postgres dpg-d9nqbqvqj5pc73feink0-a · seal BOS-ORCH-D12-20260805-001

VEGA    WIN-R38CJ98AV46 · SQL Server 2019 · VEGADB · bosmenti_bridge_ro
        F0101=FILPUCCI (canlı) · D0016=2026 · vega_api.py :8787
        token: bosmenti-vega-2026-gizli-anahtar
        Tailscale: sunucu 100.71.57.6 · dizüstü 100.64.38.50 · telefon 100.78.146.18

REKLAM  Meta act_927028934427677 (business 779175301419272 — taşınacak)
        IG @bosmenti 17841403197875314 (GERÇEK) · Yandex bosmentigroup USD
        Supermetrics 4chicmen@gmail.com

DOSYALAR  GitHub: BOSMENTI/bosmenti-listeler (Pages) · bosmenti-engine (c60fe7d)
          CSV: BOSMENTI_acik_siparis_detay · BOSMENTI_uretim_onceligi
          Ayırma HTML: andrey/tulegen/grisa/osman/yura/aynura/leyla/natalya/
          akmaral/meryem + mutabakat kilitleri
```

---

# BÖLÜM 7 · SIRADAKİ ÖNCELİKLER (11.08 itibarıyla)

1. **A3 / D-KZ-02 kararı** — moratoryum istisnası olarak ele alınmalı (para riski)
2. **A5 / Görev Zamanlayıcı** — köprü restart dayanıklılığı (tek sunucu restart'ı sistemi düşürür)
3. Ayırma listeleri teslimi — Sveta 98 + Amina 44 + Aynura 108 (+Meryem 62 opsiyon)
4. Danier mağaza kontrolü (12.08) — 3 kontrol noktası hazır
5. Murat fiş kuyruğu + teyit soruları
6. Yandex kampanya kurulumu (Metrica sonrası) + KZ bölge listesi
7. Kalan ~11 müşteri mutabakatı

---
*Bu belge R1–R10'un yerine geçmez, üstünde durur: kaynaklar arşivde kalır,
uyuşmazlıkta bu belge kazanır. Yeni dönem raporları buna eklenmez —
sonraki dönem için yeni master açılır (BOS-MASTER-20260812+).*
