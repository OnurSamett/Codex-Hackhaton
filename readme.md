# EventHopper — AI-Powered Event-Driven Travel Planner

**"Not another AI travel guide. An AI travel optimizer."**

Bu repo, "Build Anything with Codex Hackathon" kapsamında geliştirilen EventHopper projesinin kaynak kodlarını içerir. Projenin amacı, $100 Codex kredisini[span_0](start_span)[span_0](end_span) kullanarak kullanıcının girdiği serbest metin gezi taleplerini (bütçe, zorunlu etkinlik tarihi, opsiyonel rotalar) yapılandırılmış verilere dönüştürmek ve bu kısıtlamalar etrafında optimize edilmiş, fiyatlandırılmış seyahat alternatifleri sunmaktır.

## 📂 Proje Mimarisi ve Klasör Yapısı

Kod çakışmalarını önlemek için proje iki tamamen izole edilmiş dizine ayrılmıştır:

*   `/backend`: Onur Samet'in çalışma alanı. .NET 8 / Python optimizasyon motoru ve API uçları.
*   `/frontend`: Atlas'ın çalışma alanı. Vibecoding ile geliştirilen UI, API entegrasyonu ve State yönetimi.

---

## 👨‍💻 Takım ve İş Dağılımı

### Onur Samet (Core Architecture & Optimization Engine)
**Odak:** Veri yapıları, rota hesaplama algoritması ve yeniden optimizasyon (Reoptimization).
*   **Görev 1 (Mock Data):** Gerçek zamanlı API limitlerine takılmamak için İstanbul, Singapur (SWITCH etkinliği), Bangkok ve Taipei için gerçekçi uçuş/otel fiyat ve saatlerini içeren statik bir JSON veri seti oluşturmak.
*   **Görev 2 (Engine):** Zorunlu tarih kısıtlamasını (Hard Constraint) merkeze alarak A, B ve C rota alternatiflerini maliyetleriyle hesaplayan API ucunu yazmak.
*   **Görev 3 (Reoptimization):** Kullanıcının parametre değiştirmesi (örn. "Bütçeyi düşür", "Taipei'yi çıkar") durumunda mevcut kısıtlamaları baz alıp rotayı anında yeniden hesaplayan mantığı kurmak.

### Atlas (Product, Vibecoding & Pitch)
**Odak:** Kullanıcı arayüzü, Codex entegrasyonu ve sahne sunumu.
*   **Görev 1 (UI/UX):** Cursor/v0 kullanarak modern bir sohbet ekranı ve Onur'dan gelen verileri görselleştiren şık "Rota Alternatifi" kartları inşa etmek.
*   **Görev 2 (Codex Prompting):** Kullanıcının doğal dil girdisini Codex'e gönderip, arka planın anlayacağı strict JSON formatına dönüştüren ara katmanı (Prompt Engineering) yazmak.
*   **Görev 3 (Pitch):** Saat 16:15'teki jüri sunumu için 2 dakikalık kusursuz bir senaryo hazırlamak ve projenin ticari potansiyelini öne çıkarmak.

---

## 🔄 Çalışma Akışı (Workflow)

1.  **API Contract First:** Onur kodlamaya başlamadan önce, backend'in frontend'e döneceği JSON yapısının (Mock) sahte bir örneğini oluşturup `/frontend/mock-response.json` dosyasına koyar.
2.  **Paralel Geliştirme:** Atlas, bu sahte JSON'u sanki gerçek API'den geliyormuş gibi kullanarak UI kartlarını ve animasyonlarını geliştirmeye başlar. Bu sırada Onur, o JSON'u gerçek bir algoritmaya bağlayacak arka planı yazar.
3.  **Birleştirme:** Öğleden sonra, önyüzdeki sahte JSON veri kaynağı, gerçek API endpoint'i (örn. `http://localhost:5000/api/optimize`) ile değiştirilir.

---

## 🤖 Sistem Promptları (Atlas'ın Kullanımı İçin)

Aşağıdaki promptlar, Atlas'ın frontend inşa ederken ve Codex'i entegre ederken kullanacağı temel talimatlardır.

### 1. Codex Intent Parser Promptu
*Kullanıcının yazdığı metni backend'in beklediği formata çeviren sistem talimatı.*

> "Sen bir seyahat optimizasyon asistanısın. Kullanıcının verdiği serbest metinli seyahat planını analiz et ve aşağıdaki JSON formatında, sadece JSON olarak çıktı ver. Hiçbir ek açıklama yapma.
> 'hard_constraints' içine kesinlikle değişmeyecek tarih ve etkinlikleri, 'soft_constraints' içine esnetilebilir bütçe ve lokasyonları koy.
> 
> Örnek Çıktı Formatı:
> {
>   "origin": "Istanbul",
>   "travel_window": {"start": "2026-10-21", "end": "2026-11-01"},
>   "hard_constraints": [{"event_name": "SWITCH Singapore", "dates": ["2026-10-27", "2026-10-28", "2026-10-29"]}],
>   "soft_constraints": {"optional_destinations": ["Bangkok", "Taipei"], "max_budget_try": 100000}
> }"

### 2. Vibecoding (Cursor/v0) UI Promptu
*Frontend iskeletini ayağa kaldırmak için AI kod asistanına verilecek talimat.*

> "Modern, temiz ve minimalist bir yapıda bir seyahat planlama arayüzü oluştur. React ve TailwindCSS kullan. Ekran ikiye bölünmüş olmalı:
> Sol taraf: Kullanıcının doğal dille seyahat planını yazdığı ve sistemle sohbet ettiği bir Chatbox.
> Sağ taraf: Sistemden dönen JSON verisine göre oluşan 3 farklı rota alternatifini (Option A, Option B, Option C) gösteren dikey kartlar. Her kartta rotanın gidiş-dönüş şehirleri, toplam konaklama süresi ve toplam bütçe dökümü yer almalı. Kartların tasarımı sade olmalı ve kullanıcı fiyat kırılımlarını (uçuş ve otel) görebilmek için karta tıklayabilmeli.
> Öncelikle sahte bir JSON verisi (mock data) tanımla ve bileşenlerin bu veriyle nasıl render edildiğini göster."