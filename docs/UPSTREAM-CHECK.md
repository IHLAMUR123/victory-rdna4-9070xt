# Upstream kontrolü — 27 Kasım 2026

Bu not, yayınlanan `veh-324` build'inin (bu repo) 27 Kasım 2026 akşamındaki upstream durumuyla karşılaştırmasıdır. Ölçümler aynı PC'de (RX 9070 XT, 7800X3D, 31 GB RAM) alındı; oyun durumu (görev/sahne) her çalıştırmada aynı değildi, bu yüzden FPS farkları kesin değil.

> **AI notu:** Bu kontrol ve testler bir AI kodlama ajanı (OpenCode / DeepSeek V4.1 Flash) tarafından, kullanıcının yönlendirmesiyle yapıldı. Upstream kod insan geliştiricilerin; krediler README'de.

## Bizim build nerede?

| | |
|---|---|
| Bizim en iyi | `cool_fox-fixes` / `veh-324` @ `4363233` |
| Taban | `c9c932c` (port 7) + Belobug #324'ün manuel portu |
| İçerdiği | VEH guard, port 7, RYUKI preset, #324 |
| İçermediği | Belobug'un RX 9070 XT fix/HUD commit'i, texture upload prelude, port 2 sync, FSR4K, avatarmanz `PORT_CONSTANTS_ORDER` |
| Warm | 83.552 pipeline / 377 s / 4 bad |
| Ölçüm | 19–20 fps @ 5.8–6.2k draw; statik testte 16 fps @ ~7.7k draw |

## Upstream durumu

| Repo | Son commit | Tarih | Bizim için önemli olan | #324 var mı? |
|---|---|---|---|---|
| **Belobug / VIctory-Belobug-AMD** (`a-publier`) | `4eb6cfd` | 26 Kas | **RX 9070 XT fix + HUD** (`e942896`), **texture upload prelude** (`5d6bd84`), port 2 sync (#354), FSR4K, #324 | **Evet** |
| avatarmanz / VIctory-7900XTX | `014d52d` | 26 Kas | **`VICTORY_PORT_CONSTANTS_ORDER`** (ağaç/avize pop-in ve lamba odası flicker fix'i, varsayılan açık, ~2–5 ms), port RT (kapalı), `AMBIENT_RT` | Hayır |
| fxceless / victory-fork | `9a9091d` | 27 Kas | RT structure builder varsayılan açık ama **0.2–3 fps**; #1320/#1321/#1324/#1336 fix'leri; kart hash'leme -10 ms | Hayır |
| RYUKI-7900XT | `ba3d797` | 27 Kas | Ses çıkışları, pencere başlığı; ayar preset'i aynı | Hayır |
| Marvisak / VIctory | `63bcca4` | 25 Kas | depth bounds, null shader view | Hayır |

**Özet:** Bizim build, Belobug'un kendi 9070 XT dalının **52 commit gerisinde**. En güncel RDNA4 build'i `a-publier 4eb6cfd`; bizde olmayan 9070 fix'leri, HUD portu ve texture/flicker fix'leri onda var.

## a-publier testi (aynı PC, aynı save)

| Çalıştırma | FPS | Draw | Not |
|---|---:|---:|---|
| a-publier, otomatik Enter | **19–20** (18 örnek) | 6.6–6.9k | 90 s boyunca stabildi; HUD ve dokular iyi |
| a-publier, otomatik Enter (snapshot sonrası) | 10 (medyan) | 5.7k | 3–18 arası; görev/sahne değişti |
| a-publier, statik (sadece menü Enter'ları) | 11 (medyan) | 6.6k | 1–12 arası; aynı save farklı sahne |
| **#324, statik** | **16 (medyan)** | **7.7k** | 10–17 arası; aynı save |
| #324, otomatik Enter | 18 (medyan) | 6.6–7.0k | 14–19 |

**Sonuç:** a-publier'in HUD'u ve dokuları gözle görülür şekilde daha iyi (silahlar çarkı, para, sağlık barları çalışıyor). FPS ise sahneye göre 11–20 arasında değişiyor; #324 ile arasında **kesin bir fark yok**. Aynı save'den farklı görev noktalarına yüklenildiği için bu testler kontrollü değil. a-publier'i yayınlanan varsayılan yapmadan önce sabit bir benchmark save'i ile tekrar ölçmek gerekir.

## Yeni repolar (27 Kasım)

| Repo | Ne | RDNA4/9070 için? |
|---|---|---|
| `ToLIManl/VIctory-Hydra` | Çoklu fork birleşimi; RTX 3060'ta 11.24 fps | NVIDIA odaklı; 9070 verisi yok |
| `TaynanGT/Vice-Tay` | RTX 4090 için tuning | NVIDIA |
| `edsuuu/VI-BR-PORT` | 8 GB NVIDIA | NVIDIA |
| `FQY55kai/ZJOU-VICTORY` | fxceless + shader fix'leri, **hazır exe'li zip** | Kaynak değil; indirilmemeli |
| `Androtim/MiniVI` | 8 GB düşük VRAM | NVIDIA/AMD, 9070 verisi yok |
| `kibagami-nc/VIctory-Kibagami-NVIDIA` | Belobug tabanlı NVIDIA | NVIDIA |
| macOS/Linux portları | `halfwave50`, `LakshanSivakumar`, `Marukooh` | Bizim PC için değil |

**Yeni repoların hiçbiri RX 9070 XT için Belobug `a-publier`'dan daha iyi değil.** Çoğu NVIDIA odaklı veya hazır paket; kaynak-only değil.

## Uyarılar

- Belobug issue **#546** (RX 9070 XT, `a-publier 4eb6cfd`): `CARD_STEPS=2` + `THIN=1` ~%20 kazanç; `--console series-s` karakterin derisini kaybediyor; **`VICTORY_OUTPUT` çöküyor**. Bizim 1080p testi #324'te çalışmıştı; a-publier'de denenmedi.
- Belobug issue **#619** (RX 9070 XT, `4eb6cfd`): ilk açılışta "press A" ekranında ~15 s beklenirse abort; hikâye yüklemesi 20 dk takılabiliyor. Bizim kısa testte görülmedi; uzun test gerekli.
- Belobug issue **#143** (RX 9070 XT): ilk soğuk açılışta ~2 dk içinde fault; build `01aadd1`, bizim build değil.
- avatarmanz `PORT_CONSTANTS_ORDER` bizde ve a-publier'de yok; ağaç/avize pop-in'i için gerekli. Maliyeti ~2–5 ms.
- fxceless RT builder oyun için kullanılamaz (0.2–3 fps); RT kapalı kalmalı.

## Öneri

1. **Kısa vadede:** Yayınlanan `veh-324` kalsın; a-publier "aday" olarak dursun.
2. **Sonraki adım:** `a-publier 4eb6cfd` + avatarmanz `PORT_CONSTANTS_ORDER` birleştir; sabit bir save'de (görev ilerlemeyen) 2–3 dakikalık A/B ölçümü yap.
3. a-publier kazanırsa final launcher/kısayol ve README güncellenir; kazanamazsa #324 kalır.
4. `VICTORY_OUTPUT=1920x1080` a-publier'de ayrıca test edilmeli (issue #546'da çökme var).
