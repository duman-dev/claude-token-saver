<p align="center">
  <img src="banner.png" alt="Claude Token Saver Banner" width="100%" style="max-width: 1000px; height: auto;">
</p>

# <p align="center">Claude Token Saver</p>

<p align="center">
  <b>Claude Token Saver</b>, Claude ve Claude Code kullanırken token kullanımını azaltmak için pratik yöntemler sunuyor. Gerçek iş akışlarında geliştiriciler tarafından test edilmiş ve doğrulanmış — garantisi yok ama çalışıyor.
</p>

---

<h1 align="center">

[🇺🇸](https://github.com/duman-dev/claude-token-saver/blob/main/README.md) [🇷🇺](https://github.com/duman-dev/claude-token-saver/blob/main/README.ru.md) [🇩🇪](https://github.com/duman-dev/claude-token-saver/blob/main/README.de.md) [🇹🇷](https://github.com/duman-dev/claude-token-saver/blob/main/README.tr.md)

</h1>


> [!NOTE]
> Bu yöntemler uygulamadan gelen deneyim ve gözlemlerdir. Tutarlı bir şekilde çalıştığını görmüştük, ancak sonuçlar kullanım alışkanlıkları, oturum uzunluğu ve model versiyonuna göre değişebilir.

---

### Yöntemler

* [**1 — Kısa ve Doğrudan Istenme**](#1--kısa-ve-doğrudan-istenme): Claude'a dolgu cümleleri atlamasını söyle. Daha az çıktı, aynı sonuç.
* [**2 — Takip Yerine Düzenleme**](#2--takip-yerine-düzenleme): Başarısız denemeleri biriktirmek yerine değiştir.
* [**3 — Bağlam Sıkıştır**](#3--bağlam-sıkıştır): İlerlemeyi kaybetmeden uzun oturumları sıfırla.
* [**4 — Bölmek Yerine Topla**](#4--bölmek-yerine-topla): Birçok mesaj yerine ilgili görevleri bir mesajda birleştir.
* [**5 — Caveman Gibi Skill'lerle Token Optimizasyonu**](#5--caveman-gibi-skillerle-token-optimizasyonu): Girdi ve çıktıyı özel bir token optimizasyon skill'i/proxy'siyle sıkıştır.

---

## 1 — Kısa ve Doğrudan Istenme

Claude'un varsayılan stili konuşmacıdır. Her giriş, özet ve "Sana yardımcı olmaktan memnuniyet duyarım" çıktı tokenlerine maliyet — ve gelecekteki her turda tarihçenin bir parçası olarak tekrar okunur.

**Çözüm:** Bunu `CLAUDE.md` (Claude Code) veya **Ayarlar → Profil → Kullanıcı Tercihleri** (claude.ai) içine yapıştır:

```
Reply in the language user employs. Use most concise form possible.
Skip pleasantries, preambles, recaps.
No "I'd be happy to", "Great question", "Let me explain".
Drop articles, filler words wherever meaning stays clear.
Prefer short declarative sentences. If tool call needed,
run first and show only result. Do not narrate steps.
Prioritize code/data format over prose.
No explanations unless essential. Output only.
```

**Önce:**
> "Harika soru! Sana yardımcı olmaktan memnuniyet duyarım. Bu hatayı düzeltmek için 12. satırdaki null başvurusunu kontrol etmen gerekecek. Burada neler olduğunu açıklayabilirim..."

**Sonra:**
> "12. satırda null başvurusu."

Aynı cevap. Çok daha az token.

---

## 2 — Takip Yerine Düzenleme

Claude fikri kaçırdığında, tepkisel olarak düzeltme göndermek gelir: *"Hayır, kastediyorum..."* Bu düzeltme tarihçeye eklenir. Yanlış cevap kalır. Her tur sonrası her ikisi de tekrar okunur.

**Çözüm:** Cevap vermek yerine orijinal mesajını düzenle ve yeniden oluştur.

| ❌ Takip | ✅ Düzenleme |
|---|---|
| Orijinal mesaj kalır | Orijinal mesaj güncellenir |
| Yanlış cevap tarihçede kalır | Yanlış cevap atılır |
| Her turda her ikisi okunur | Sadece doğru konuşma bağlamda |

Claude Code'da: oturum yoldan çıktığında, aynı oturumu geri yönlendirmeye çalışmak yerine yeni bir oturum başlat.

---

## 3 — Bağlam Sıkıştır

Her mesaj tam konuşma tarihçesini taşır. 15–20 mesajdan sonra, o tarihçenin çoğu ilgisiz — ama yine de her turda yüklenir.

**Çözüm:** Yeni bir sohbete başlamadan önce, makine tarafından optimize edilmiş bir devredilme almak için bu istemi gönder:

```
Summarize our entire conversation with language user employs
so I can paste it into a
new chat and continue without losing context. Include:
(1) the original goal or problem
(2) key decisions made and why
(3) any code, config, or data we settled on, verbatim, in code blocks
(4) open questions and next steps
Use short sections with headings. Skip small talk and
exploratory tangents. Optimize the summary for a future
Claude reading it cold.
```

Sonucu yeni bir sohbetin ilk mesajı olarak yapıştır. 40 mesajlık bir oturum 200 sözcüklük bir özete — tarihçe taşınmaz.

---

## 4 — Bölmek Yerine Topla

Her mesaj tam konuşma tarihçesini yeniden yükler. Üç küçük görev ayrı gönderildiğinde = üç tarihçe yüklemesi. Üçü birden = bir yükleme.

**Çözüm:** İlgili istekleri tek bir mesajda birleştir.

**Önce:**
```
Sen: Bu fonksiyondaki yazım hatasını düzelt
Sen: Şimdi bir docstring ekle
Sen: Ve tür ipuçları ekle
```

**Sonra:**
```
Sen: Yazım hatasını düzelt, docstring ekle ve tür ipuçları ekle
```

Yan fayda: Claude tam resmi bir kez gördüğü için çıktılar daha tutarlı olur.

---

## 5 — Caveman Gibi Skill'lerle Token Optimizasyonu

Yukarıdaki yöntemler, Claude'u *nasıl kullandığından* kaynaklanan token israfını azaltır. Özel bir skill, konuşmanın her iki tarafında da *neyin gönderildiğini* sıkıştırabilir.

[**Caveman**](https://github.com/juliusbrussee/caveman), ajan çıktısını sıkıştıran (daha kısa, "mağara adamı" tarzı yanıtlar — bazı görevlerde bildirilen ~%87 azalma) ve isteğe bağlı yerel proxy'si aracılığıyla girdiyi de modele ulaşmadan önce sıkıştıran açık kaynaklı bir skill/eklentidir (JSON, loglar, kod diff'leri ve uzun metinler özlerine indirgenir; orijinaller gerektiğinde yerel olarak geri alınabilir).

**Çözüm:** Skill'i kur ve oturum bazında etkinleştir.

```bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/v1.10.0/install.sh | bash
```

Ardından Claude Code'da:

```
/caveman [lite|full|ultra|off]
```

Tasarruflar iş yüküne göre değişir — projenin benchmark'larını bir garanti değil, başlangıç noktası olarak değerlendir ve gecikme veya maliyete duyarlı işlerde güvenmeden önce kendi baseline'ınla karşılaştır.

---

### Katkıda Bulun

Gerçekten çalışan ve doğrulanabilen bir yöntem buldunuz mu? Bir **Issue** açın veya **Pull Request** gönderin.

---

### Lisans

MIT
